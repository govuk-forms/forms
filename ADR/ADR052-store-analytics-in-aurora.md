# ADR052: Store analytics in a separate Aurora PostgreSQL database

Date: 2026-09-07

## Status

Accepted

## Context

We want to measure key performance indicators for GOV.UK Forms, starting with:

- the number of submissions over time
- the number of live and archived forms over time

We need at least 2 years of data for year-on-year comparisons. Longer retention is preferred provided it is cheap and efficient for us.

These are business analytics rather than operational monitoring. Accuracy matters. The data does not need to be real-time, but having up-to-date figures is useful.

forms-runner already logs a structured `form_submission` event for every submission.

Recording the number of submissions per form and the number of forms per organisation is not a necessity, but the granularity would be useful if we later wanted to reuse the metrics for form-level analytics, for example showing per-form statistics in forms-admin.

### Options considered and rejected

**CloudWatch OpenTelemetry metrics (tried).** This is AWS's current, recommended metrics model, billed per GB ingested with 15 months of retention. Metrics are statistical aggregates, not a record of events, so should not be used for anything that needs exact figures. For example, counters are aggregated in-process and exported on an interval, so counts are lost if a task exits before export, and PromQL `rate()` and `increase()` extrapolate across the query window and can return non-integer results. The OpenTelemetry SDK also caps each metric at 2,000 attribute combinations by default, after which per-form measurements collapse into a single overflow bucket.

**CloudWatch Classic metrics.** Every unique dimension combination is a separately billed metric at $0.30 per metric per month, so a per-form dimension costs roughly $0.30 per form per environment per month and grows with every form we host. Metrics expire after 15 months.

**Google Analytics 4.** Client-side events only fire after a user accepts usage cookies, so they undercount. The server-side Measurement Protocol requires a user id on every event, so we would have to create pseudo ids. Sending submissions with fabricated ids either inflates user counts or lands them under "(not set)", polluting our real user metrics. A per-form dimension exceeds GA4's 500-value high-cardinality threshold, so less common forms are condensed into an "(other)" row. Event-level data is retained for at most 14 months on a standard property.

**Splunk.** We keep logs for 12 months, it is not a tool we own, and we intend to move away from it.

**CloudWatch Logs Insights over existing logs.** This needs no new infrastructure, but every query scans the whole runner log group rather than just submission events, so we pay for and wait on data we do not need. Keeping the whole log group for years to preserve a handful of events would be wasteful.

**The forms-runner database.** This would grant observability tooling access to a database with potentially sensitive information, and analytics queries could affect production performance.

**Amazon Redshift Serverless or OpenSearch.** Both are capable, but their cost far exceeds what a few small tables need.

**Amazon S3 Tables, queried with Amazon Athena (prototyped).** S3 Tables is storage optimised for analytics workloads, storing tables in Apache Iceberg format, and Athena is a serverless query engine that can read them. A working prototype fed the table from the structured log lines the applications already emit, using a CloudWatch Logs subscription filter, a Lambda transform and Kinesis Data Firehose, so it needed no application changes. It is very cheap, less than $10 per year per environment, and fully managed.

However:

- S3 Tables, Firehose and Athena are all unfamiliar services to the team, and the pipeline has several moving parts to understand and maintain.
- Firehose delivery is at-least-once, so queries would need de-duplication or the transform would need an idempotency key.
- Table schemas are defined in Terraform, but the provider does not support partitioning yet.
- Athena queries take seconds, too slow for the request path. If we wanted to extend this to showing per-form statistics in forms-admin would need a separate, faster way to query the same data.

## Decision

We will store analytics in a new Aurora PostgreSQL cluster, separate from the forms-admin and forms-runner databases, holding only analytics tables. Each environment gets its own cluster.

The applications write events to it directly through a second Rails database connection, rather than deriving events from logs. Only non-sensitive fields are stored: form identifiers and metadata, never answers or personal data.

We chose Aurora because:

- It is familiar. We already run Aurora PostgreSQL, so there are no new services to learn and we can reuse existing Terraform, backup and access patterns.
- Writes are transactional and exactly-once, with no log parsing or de-duplication, which suits data where accuracy matters.
- Indexed rows give low-latency queries, so the same store can later serve per-form analytics inside forms-admin.
- It is isolated from production databases, so analytics queries cannot affect production performance.
- We can set our own retention limits, and the data is straightforward to export if we later need to move it elsewhere.
- It can be queried by data visualisation tools, including Grafana's PostgreSQL datasource.

## Consequences

- Always-on cost, about $500 per year per environment, as the cluster must run whenever we accept submissions.
- More to manage: engine upgrades, backups and credentials for another cluster in each environment.
- Durability depends on our own backup and restore, so the cluster must be included in our existing backup arrangements.
- A row store is not designed for analytical queries. This is not a problem at our current scale, and the data can be moved to a columnar store such as S3 Tables if it becomes one.
- The applications need a second database connection and a small amount of code to record events, so this is not a zero-change option.
- We also need to implement a way to visualise this data.
