# Let form fillers get a copy of their answers

## Status

- Date released: 6 July 2026
- [Epic Trello card](https://trello.com/c/VG8QsupG/86-send-form-fillers-answers-to-them)
- [Copy of form filler’s answers feature document](https://docs.google.com/document/d/1pysSbSXhN6yJlpWnFFw1M6SkeLsacZTUNyC_KKz-8eo/edit?pli=1&tab=t.0) 
- [Mural board](https://app.mural.co/t/gaap0347/m/gaap0347/1770650530483/cb5b4a5307830bcb80a2969ee734560afbc7e098)

## Contents

- [What is this feature?](#what-is-this-feature)
- [Key decisions](#key-decisions)

## What is this feature?

Form fillers may want to get a copy of their answers, and form creators may want to allow this. Currently, there’s no way for this to happen. 

We should let form fillers get a copy of their answers if they want them, and if form creators are willing to allow this.

### As-is

Form fillers can choose to get a confirmation email after submitting their form. There’s no way for them to request a copy of their answers.

Form creators may want to let people get a copy of their answers, but there’s no way for them to enable this.

### To-be  

Form creators will be able to let people request a copy of their answers, though they won’t be able to make this mandatory. 

Form fillers will be able to request a copy of their answers in the optional confirmation email that’s sent after their form’s been submitted. 

To help make this more secure, we’ll require form fillers requesting a copy of their answers to log into their GOV.UK One Login account, or create a new one. They’ll be able to use the email address linked to their account to receive the confirmation email containing a copy of their answers. 

## Key decisions

Because of the data security considerations when emailing potentially sensitive submission data, we needed a way of making sure the data was sent to a verified email address.

Following a hackathon with GOV.UK One Login, we decided to use One Login’s email/password sign in for this purpose. 

This was partly because it lets us reuse GOV.UK products rather than creating something new, but also because One Login will be GOV.UK’s ‘sign in’ in the future - and it’s already the sign-in functionality for the GOV.UK App. 

The thinking was that form fillers who are already signed in or using the app will, in theory, have a smoother journey using this method. GOV.UK One Login integration will also be useful for GOV.UK Forms going forward as we can use its authentication for further features.

### Scope 

We agreed the following:
  	
- We’ll make it optional for form fillers to get a copy of their answers initially, and will monitor form creator usage/feedback on this
- The submission email to form creators won’t specify whether form fillers have opted to receive a confirmation email or not - we’ll wait for feedback from form creators about this
- We’ll use SES to send the confirmation email with a copy of someone’s answers

### Design decisions

The following points were considered during and after the hackathon with GOV.UK One Login. 

#### Where in the user journey should form fillers be able to request a copy of their answers? 

We considered various possibilities about where the option to request a copy of their answers should be presented to form fillers. 

These included:

- The ‘Check your answers’ page
- The start of the form
- After submitting the form (this would not work well with adding a payment link)
- A new page before the ‘Check your answers’ page

In the end, we decided to design a new page that form fillers would see after answering all the form’s questions but before the ‘Check your answers’ page where a form is submitted. 

#### Should we give form creators the option to turn this feature on for a form?

Initially we decided to turn this new feature on for all forms - so all form fillers would get the new page and the option to get a copy of their answers. 

We then reconsidered this and explored making it optional for form creators to add this to a form. That way, they could decide if form fillers should get this choice or not, depending on how relevant or useful it might be for their particular users.

The potential benefits of making this optional for form creators are that it would:

- allow the form creator to avoid the risk of their form fillers getting distracted or lost in the GOV.UK One Login journey unnecessarily - for example, if it’s not really relevant or useful for a specific form
- give us the opportunity to make sure they're aware of what this feature means for the form filler, when it might be useful to turn it on, and what the potential risks are
- place the responsibility for the choice and risk with the form creator rather than with GOV.UK Forms
- mean that this would not be automatically turned on for all forms, which would reduce risk generally for a new feature

This led to our decision that even if the feature is turned on by the form creator, form fillers should still get a choice about whether they want to receive a copy of their answers.

#### What if a form filler doesn’t complete the GOV.UK One Login part of the journey?

Our main concern after the Hackathon was what happens if the form filler doesn’t manage to complete the GOV.UK One Login part of the journey - they won’t get taken back to the form and will still not have submitted it. 

We considered ways to mitigate this risk and reconsidered the journey, but ended up sticking with our plan. 

We raised a feature request to improve this with GOV.UK One Login, but it isn’t likely to be dealt with very quickly. For now, we’ve tried to mitigate this risk as much as possible by outlining the potential risk in the new content for form creators. 

We decided to be explicit about the fact that this risk exists and that it’s for form creators to decide if they feel it’s a risk worth taking. By adding a checkbox to the new ‘Give people the option to get a copy of their answers by email’ page we aimed to reduce the chance of form creators enabling this feature without considering the possible consequences. The checkbox label is ‘Give people the option to get a copy of their answers by email - I’m ok with the risk’.

#### How might this affect routing-related content?

We agreed we’d need to make content changes to the way we talk about the place routes go to if someone goes to the end of the form. Up until now, we’ve said ‘Go to the check your answers page’, but this won’t work once the option to get a copy of your answers is turned on. 

We decided to change the wording to something more generic to allow for this.

We asked the devs to confirm whether any issues might be created by some forms having the page with this option and some not. They didn’t feel that we’d need to make any extra changes just because we’re making the feature opt-in. They planned to do a spike for changes to the runner as there would be different approaches to this.

## Designs and content

This feature required a combination of new content and iterations to existing content, for both form creators and form fillers. 

Welsh translations were also needed for new form-creator-facing content.  

### Form-creator journey - summary

The form-creator facing designs and content that were created or iterated for this feature were: 

- ‘Create a form’ task list page - new optional task (iteration)
- ‘Give people the option to get a copy of their answers by email’ page (new)
- ‘Add information about what happens next’ page - new sentence if someone wants a copy of their answers (iteration)
- Live form view - new section if people can ask for a copy of their answers (iteration)
- Routing-related content - refers to the ‘end of the form’ instead of ‘Check your answers before submitting’ (iteration)

The form-creator journey for this feature starts with a new optional task on the task-list page. If selected, this leads to a new page that allows form creators to give people the option to get a copy of their answers by email. 

If a form creator selects the checkbox and clicks ‘Save and continue’, a success banner confirming that people will be able to get a copy of their answers appears. If they click ‘Save and continue’ without selecting the checkbox, a success banner confirming that people will not be able to get a copy of their answers will show instead.

If the feature is enabled, the ‘what happens next’ page will include a new sentence stating that a form filler’s answers will be added to a confirmation email when they’ve submitted their form. The ‘live form view’ page will also display a new section stating that ‘People can ask for a copy of their answers’.

The terminology used when routing someone to the end of the form has also changed - it’s now more generic (‘end of form’) rather than sending someone to the ‘Check your answers’ page, as this no longer works in all use cases. 

### Form-filler journey - summary

Form-filler facing designs and content that were created or iterated for this feature were: 

- ‘Do you want to get an email with a copy of your answers?’ page (new)
- ‘Use GOV.UK One Login to keep your information secure’ page (new) - only for those who choose to get a copy of their answers 
- ‘Check your answers before submitting’ page (iteration) - 2 different versions, depending on whether someone wants a copy of their answers or not 
- ‘Your form has been submitted’ page (iteration) - only for those who choose to get a copy of their answers (iteration)
- Confirmation email with copy of a form filler’s answers (iteration) - email includes answers, if someone’s requested this

The form-filler journey now varies depending on whether someone chooses to get a confirmation email with a copy of their answers or not, if enabled. 

If they want a copy of their answers their journey will involve being taken through the GOV.UK One Login journey before being returned to GOV.UK Forms to submit their form. 

If they request a copy of their answers, their journey will be: 

Finish answering questions → ‘Do you want to get an email with a copy of your answers?’ page → ‘Use GOV.UK One Login to keep your information secure’ page → GOV.UK One Login screens → back to GOV.UK Forms ‘Check your answers before submitting’ page → ‘Your form has been submitted’ page

If they decide to not get a copy, their journey will be:

Finish answering questions → ‘Do you want to get an email with a copy of your answers?’ page → ‘Check your answers before submitting’ page → ‘Your form has been submitted’ page

