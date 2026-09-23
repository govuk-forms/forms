# ADR054: Store saved answers in the forms-runner database

Date: 2026-09-15

## Status

Accepted

## Context

For the "Save & Return" feature, we will allow form fillers to log in using GOV.UK One Login to save their progress and return up to 30 days later to continue filling a form.

We will add a link to each question page to allow the user to save their data. If they save their data and then continue with their form, we will not save the data against their One Login account until they opt to do so again. Any unsaved data will expire after 20 hours of inactivity, which is the existing behaviour.

## Decision

When a form filler saves their form progress, we will save a snapshot of their answers to the forms-runner PostgreSQL database. We will link the data to the user's One Login account using the `sub` property returned by the One Login API, which is a unique identifier that does not change.

When the user continues answering questions after they've saved their progress or returned to the form, we will continue to store their answers in their session in Redis until they save their progress again.

We will encrypt the database column that contains the answers at the Active Record level, as we do for the `submissions` table.

We will store a hash of the user's email address against their saved answers so we can identify the data to delete if a user contacts us to request deletion. This will allow us to find the relevant data, but not read the user's email address.

Records will be deleted:
- when the user submits the form
- 30 days after the saved progress was last updated

## Consequences

- We will be storing user data for a longer time, including forms that are never submitted.
- Developers with access to the support role in the production environment will be able to access answers for incomplete forms if we add a rake task that decrypts the data. They already have access to submitted form data via a rake task that is used only when data is malformed.
- Developers with access to the admin role in production will have access to the encryption key, which would allow them to decrypt answers for incomplete forms.
- We will store a hash of the user's email address against the data. This means the email address will not be readable, but if someone has knowledge of a user's email and is able to access and decrypt the data, they can associate an incomplete submission with an individual.
