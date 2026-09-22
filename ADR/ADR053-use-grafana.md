# ADR053: Use Grafana

Date: 2026-09-08

## Status

Accepted

## Context

GOV.UK Forms has no central tool for visualising data. We want to show our key performance indicators (KPIs), which will likely be stored in a SQL database, to the whole team.

We currently have 2 options for dashboards. Both have limitations:

1. Splunk is limited to the logs stored in Splunk. We do not own it, and we want to move away from it so we can centralise our observability stack.
2. CloudWatch dashboards are limited to CloudWatch metrics. Connecting other data sources is difficult, and the dashboards are expensive.

Grafana is already used by GOV.UK, GOV.UK Notify, GOV.UK Pay and Emergency Alerts.

## Decision

We will use Grafana as our default tool for dashboards and data visualisation.

We can add data sources for our KPIs and for CloudWatch, where our other signals already live. This opens up the possibility of showing other data in the same place, such as Zendesk analytics and FinOps data.

Grafana will be available to the whole team, not only engineers.

### Implementation options

This decision does not cover how we implement Grafana. However, there are 2 options for reference:

- Amazon Managed Grafana (AMG) is a fully managed solution. It is currently limited to Grafana version 12, while the latest release is version 13. Sign-in is only available through SAML or IAM Identity Center. EE have not set up Entra or Identity Center yet, so we cannot use AMG today. It is priced per user and would cost roughly $1,000 a year for the production environment (for example, 3 editors and 10 viewers).
- Self-hosting open source Grafana on ECS would need an ECS Fargate task, an Application Load Balancer (ALB) and an Aurora PostgreSQL database for Grafana's own state. We can run the latest version of Grafana and use GitHub sign-in for now, as GOV.UK and GOV.UK Notify do. GOV.UK Pay also self-hosts. It would cost roughly $1,000 a year for the production environment.

The 2 options are similar enough that cost does not decide between them. We have confirmed that the organisation is happy with this level of spend.

We have not considered Grafana Cloud or other third-party offerings, as they would need a procurement process.

## Consequences

### Positive

- One place for our data. KPIs, CloudWatch metrics and other data can be shown in the same tool.
- Consistent with other GOV.UK teams. This makes it easier for engineers to move between teams and share knowledge. It also makes it easier if we ever centralise this tooling more widely.
- Supports a wide range of data sources. Potential use cases could be Zendesk analytics and FinOps data.

### Negative

- Another service to run.
