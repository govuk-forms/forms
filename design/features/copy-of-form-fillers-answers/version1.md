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
