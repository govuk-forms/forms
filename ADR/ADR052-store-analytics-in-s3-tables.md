# ADR052: Store analytics in Amazon S3 Tables and query them with Amazon Athena

Date: 2026-09-07

## Status

Accepted

## Context

We want to measure key performance indicators for GOV.UK Forms, starting with:

- the number of submissions over time
- the number of live and archived forms over time

We need at least 2 years of data for year-on-year comparisons. Longer retention is preferred provided it is cheap and efficient for us.

These are business analytics for an internal dashboard rather than operational monitoring. Accuracy matters. The data does not need to be real-time, but having up-to-date figures is useful.

forms-runner already logs a structured `form_submission` event for every submission.

Recording the number of submissions per form and the number of forms per organisation is not a necessity, but the granularity would be useful if we later wanted to reuse the metrics for form-level analytics.

There is demand from form creators for more analytics, such as funnel analytics and metrics for save and return. Some of these may also be useful for internal performance analytics, so a single way of recording events for both would avoid maintaining two. However, what we will offer form creators is not yet clear or prioritised. Funnel analytics need to follow users across interactions, which may be better served by Google Analytics or a purpose-built product analytics tool than by our own storage.

We want to get something working quickly for the requirement we understand today, at low cost and with little to maintain, and revisit if we outgrow it.

### Options considered and rejected

**CloudWatch OpenTelemetry metrics (tried).** This is AWS's current, recommended metrics model, billed per GB ingested with 15 months of retention. Metrics are statistical aggregates, not a record of events, so should not be used for anything that needs exact figures. For example, counters are aggregated in-process and exported on an interval, so counts are lost if a task exits before export, and PromQL `rate()` and `increase()` extrapolate across the query window and can return non-integer results. The OpenTelemetry SDK also caps each metric at 2,000 attribute combinations by default, after which per-form measurements collapse into a single overflow bucket.

**CloudWatch Classic metrics.** Every unique dimension combination is a separately billed metric at $0.30 per metric per month, so a per-form dimension costs roughly $0.30 per form per environment per month and grows with every form we host. Metrics expire after 15 months.

**Google Analytics 4.** Client-side events only fire after a user accepts usage cookies, so they undercount. The server-side Measurement Protocol requires a user id on every event, so we would have to create pseudo ids. Sending submissions with fabricated ids either inflates user counts or lands them under "(not set)", polluting our real user metrics. A per-form dimension exceeds GA4's 500-value high-cardinality threshold, so less common forms are condensed into an "(other)" row. Event-level data is retained for at most 14 months on a standard property.

**Splunk.** We keep logs for 12 months, it is not a tool we own, and we intend to move away from it.

**CloudWatch Logs Insights over existing logs.** This needs no new infrastructure, but every query scans the whole runner log group rather than just submission events, so we pay for and wait on data we do not need. Keeping the whole log group for years to preserve a handful of events would be wasteful.

**The forms-runner database.** This would grant observability tooling access to a database with potentially sensitive information, and analytics queries could affect production performance.

**Amazon Redshift Serverless or OpenSearch.** Both are capable, but their cost far exceeds what a few small tables need.

**A separate Aurora PostgreSQL cluster for analytics.** A new cluster in each environment holding only analytics tables, with the applications writing events directly through a second Rails database connection. It is familiar, writes are transactional and exactly-once, and indexed rows would give low-latency queries suitable for serving per-form analytics in forms-admin later. However, it is always on, costing about $500 per year per environment, and adds engine upgrades, backups and credentials to manage for each environment. That is a lot of cost and maintenance for a few small tables behind an internal dashboard.

**Store metrics in the forms-admin database.** forms-runner would send events to forms-admin, which would store them in new tables. The same data could replace CloudWatch as the source of the metrics we show form creators, and forms-admin could offer a simple dashboard and download for internal use. This would give one way of recording metrics for both form creators and our own KPIs, using a familiar database with no new infrastructure. But it means building and maintaining a metrics API, new tables and dashboards, alongside the Grafana we are adopting for observability anyway. It also tightens the coupling between forms-runner and forms-admin when we are trying to reduce it. This is more effort than today's requirement needs, and moving to it later should be cheap once we know what analytics to offer form creators.

## Decision

We will store analytics in Amazon S3 Tables and query them with Amazon Athena.

Amazon S3 Tables is storage optimised for analytics workloads. Tables are stored in Apache Iceberg format and can be queried by any engine that supports Iceberg. Athena is a serverless query engine that supports Iceberg and S3 Tables.

Each environment gets an S3 table bucket with a `forms` namespace, and table schemas are defined in Terraform.

Initially, events are taken from the structured log lines the applications already emit: a CloudWatch Logs subscription filter selects them, a small Lambda transform maps them to the table schema, and Kinesis Data Firehose writes them to the table. This needs no application changes, and we have a working prototype. Later, the applications could write events directly, removing the dependency on the log format.

Only non-sensitive fields are stored: form identifiers and metadata, never answers or personal data.

We chose S3 Tables and Athena because:

- It is very cheap, less than $10 per year per environment. The main costs are S3 storage and Athena's per-query charge.
- It is fully managed. S3 Tables handles compaction and snapshot maintenance, and Firehose, Lambda and Athena have no servers to run.
- It has S3 durability and availability, with no backups for us to manage.
- It is isolated from production databases, so analytics queries cannot affect production performance, and access is controlled with IAM.
- A columnar store suits analytical queries, and will continue to as the data grows.
- It is quick to get going, as the prototype already works and needs no application changes.
- We can set our own retention limits, and the data is in an open format that is straightforward to move elsewhere if we need to.
- It can be queried by data visualisation tools, including Grafana's Athena datasource.

## Consequences

- S3 Tables, Firehose and Athena are new to the team, though the pipeline is small and fully managed.
- Firehose delivery is at-least-once, so duplicate records are possible. This is rare, and can be handled in queries or with an idempotency key in the transform.
- Athena queries take seconds. This is fine for an internal dashboard, but if we later want per-form statistics in forms-admin we would need a faster way to query the data.
- There is some additional vendor lock-in, although we are already heavily invested in AWS and the data is in an open format.
- We also need to implement a way to visualise this data.
