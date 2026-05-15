# ADR050: Use Amazon SES to send confirmation emails

Date: 2026-05-13

## Status

Accepted

## Context

We currently use GOV.UK Notify to send confirmation emails to form fillers when they submit a form. We already use
Amazon SES to send submission emails to processing teams. We previously used Notify for sending submission emails, but
switched to using SES as we needed to be able to attach files to emails - see [ADR019: Use Amazon
SES](ADR019-use-amazon-ses.md). An additional benefit was SES gave us more control over the formatting of emails, which
allowed us to improve how we display the answers.

We are adding a feature to allow form fillers to receive a copy of their answers in the confirmation email. We want to
use the same formatting for the answers in both submission and confirmation emails rather than having to maintain two
different formats for SES and Notify.

## Decision

We will use Amazon SES to send confirmation emails to form fillers when they submit a form, instead of GOV.UK Notify.

Initially, this will just be when they opt to receive a copy of their answers, but we should also make this change for
confirmation emails that don't include the answers in the future for consistency.

We will continue to use GOV.UK Notify for sending emails to admin users.

## Consequences

Pros:
- We only need to maintain one format for the answers in the emails, rather than needing to support separate formatting
  for use in Notify.
- We will be able to attach uploaded files to confirmation emails if we want to in the future.
- We are only reliant on one email provider for the form filler journey, which should be easier to maintain.

Cons:
- We will no longer be able to use the Notify interface to check whether emails were successfully sent. However, we have
not used this often for support.
- We will need to handle bounces and complaints for confirmation emails in SES. We shouldn't need to take any action on
  these, but we will need to ensure our reputation as a sender isn't negatively impacted.