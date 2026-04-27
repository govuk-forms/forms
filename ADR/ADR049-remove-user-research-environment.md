ADR049: Remove user research environment

Date: 2026-04-16

## Status

Accepted

## Context

We run a separate user research (UR) environment: a copy of GOV.UK Forms in its own AWS account. It used to cost about $850 per month; after reducing database spend it now costs about $350 per month ($4,200 per year).  There is little further scope to reduce costs without removing parts of the infrastructure, which creates overhead before we can start using it.

We have not used it in the last 12 months, and there are no plans to use it in the short term.

We have also built pull request (PR) preview environments, which provide a convenient way to see and interact with changes before they are merged. 

The main benefits of the UR environment were that it was isolated from other environments, could be kept stable (protected from change) while we conducted user research, and allowed testing of larger changes (e.g. deploying multiple applications or changes to infrastructure).

We also have dev environment which is seperate to our deployment pipeline (i.e. not staging and production). It could be used in lieu of the UR environment.

## Decision

We will remove the user research environment and its AWS infrastructure.

The ongoing cost in money, maintenance time, and operational overhead is now disproportionate to the value it delivers, especially given recent usage.

If and when we need we could do the following:
- freeze deployments to the dev environment when we need a stable environment for research
- use PR preview environments for smaller, single app changes
- improve preview environments to support more complex multi-app or infrastructure changes where needed

## Consequences

- Saves about $350 per month ($4,200 per year).
- Reduces operational overhead and platform complexity.
- Means some larger changes will need more coordination to run UR in dev, or further investment in preview environments.
