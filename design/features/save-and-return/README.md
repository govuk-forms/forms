# Save and return 

## Status 

Current version: 1  
Date updated: *2026-12-27*  

## Contents

- [Status](#status)
- [Contents](#contents)
- [What](#what)
  - [As-is](#as-is)
  - [To-be](#to-be)
- [Key decisions](#key-decisions)
- [Designs and implementation](#designs-and-implementation)
  - [Admin: Form creator journey](#admin-form-creator-journey)
  - [Runner: Form filler journey](#runner-form-filler-journey)
- [Research](#research)

___

## What

### As-is 

Currently form fillers have 20 hours to complete a form with all relevant information. This 20 hours time limit is due to how long the service stores their inputs in session data. 

### To-be  

Form creators should have the option to allow their form fillers to be able to complete a form over a longer period of time. This might be due to: 
- asking for complex information
- requiring different pieces of documentation identification codes, or copies
- a form being a part of a longer process that happens over a longer period of time, such as applications that require different licences or certain gates to be passed

Form fillers should be able to complete a form in a timely manner that works for them. A form filler might have started a form and then been asked for a piece of information or supporting evidence that they don’t currently have to hand. They should therefore be able to save and return to the form at a later date on a different device, if required.  

___   

## Key decisions 

There were several decisions made by the team that were either to allow earlier release or based on departmental goals. These include but are not limited to: 
- we prioritised using GOV.UK One Login as our authentication solution for form fillers returning to a form, this associates a single form save with a persons One Login
- we will only allow one in progress form submission to be ‘saved’ per individual form. A form filler can only save a form they are filling in once, meaning agent form fillers won’t be able to use the functionality to start multiple forms and return to them later for each client or application.
- emails sent to form fillers will contain a link to return to the form, but they will still need to sign in to GOV.UK One Login to continue their form. We are not sending unique links
- saved forms will only be saved for 30 days, unless the form filler re-saves their form where the count is reset. This means a form could be in progress for an indeterminate amount of time, unless that form is archived or deleted. 
- form creators have control to turn the feature on when creating a new form or editing a draft form. They will not be able to turn the feature off at a later date. This is due to technical complexity of trying to rollback the feature where users might have already saved a form in progress and how we should handle this.
- there is no easy way for form creators to test the save and return journey without creating a real GOV.UK One Login and using that to test the form fillers journey. We do not currently know of a workaround or solution to help make this easier
- form creators will not be shown the number of ‘saved’ in progress forms as part of the metrics in our first release
- form creators will not be shown where form fillers are most likely to use the ‘saved and exit’ button in their form to help them better understand how their form is working 

### Decisions revisited and revised by the team  

- a form filler can no longer have a saved form if that form has since been archived. This was seen as likely to cause issues for both form fillers who might be wasting their time and form processors who have to deal with unexpected submissions or contacts 
- we will implement an ‘in progress’ check your answers page where a form filler returns to their form they can see what questions they have answered so far before continuing through the form

___   

## Designs and implementation    

### Admin: Form creator journey  



### Runner: Form filler journey  



___   

## Research      

[Research findings and testing](../../../research/2026-09-08-save-and0return-interviews.md)

___  

[Back to the top](#save-and-return)
