# Completed form submissions

## Contents

- [What](#what)
- [Iterations](#iterations)
___

## What

How completed forms are sent to the form owners for processing, and how form creators set this up in the platform. 

## Iterations

### Email submissions

The initial MVP of the form builder provided email as the only way to receive completed form submissions. 

The form creator has to provide an email address for them to be sent to. That email address must be verified with a code before it can receive submissions. This must be done before a form can be made live. 

When a form is completed, the answers given are sent to the email address in the body of an email. There is one email for each form completed.  

Email was the starting point for the product as it is equivalent to the way many document-based forms are delivered to government services. 

### CSV file email attachment

This iteration added the option for form creators to also get the answers in a CSV file attached to each completed form email. The task list to create or edit a form was given an extra optional task to 'Get completed forms as CSV files' for form creators to turn the CSVs on or off.

### [JSON file email attachment, December 2025](version-3-json-submissions.md)

This added the option for form creators to get form submissions in a JSON file attached to each completed form email. 

### S3 buckets, late 2025

We made it possible for completed form submissions to go to an AWS S3 bucket in 2025. Initially this is a manual process outside of the platform. The form owners have to set up the bucket and manage it themselves. They also have to choose to receive completed forms as either JSON or CSV files. When they've given us the details and we switch their form to S3, they then get completed forms sent to their S3 bucket instead of via email. 

We later published some guidance about this option on the product site - [processing completed form submissions](https://www.forms.service.gov.uk/processing-completed-form-submissions). 

### Telling people about the S3 buckets option, January 2026

We need to make several changes to the task list to properly represent the S3 option. While that is being prioritised, we wanted to do something smaller to at least tell people that this is an option.

So in January 2026, we added a details component to the page where form creators set the email address for completed forms. This tells them S3 buckets is an option, and links to the [processing completed form submissions](https://www.forms.service.gov.uk/processing-completed-form-submissions) guidance on the product site which has more information and tells them to contact us to set it up.

### [Daily batches of form submissions in CSV](version-4-daily-batch-submissions.md)

Added an option to be sent a daily email with a CSV of data from all submissions to a form from the previous day.

### [Weekly batches of form submissions in CSV](version-5-weekly-batch-submissions.md)

Added an option to be sent a weekly email with a CSV of data from all submissions to a form from the previous week.
