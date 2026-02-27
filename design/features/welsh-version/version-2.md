# Add Welsh version of a form v2 - first release

## Status

Date created: 23 February 2026   

Released  

___

## Contents

- [Status](#status)
- [Contents](#contents)
- [What](#what)
- [Designs for development](#designs-for-development)
- [Form filler screens](#form-filler-screens)

___

<br>

## What  

After our round of testing we agreed on what our first release for Welsh versions would look like.  
  
The team agreed we could go live without finishing all the designed parts of the journey and without requiring further testing of ceratin parts of the journey as these were considered low risk.  
  
This is a write up of the screens that were developed for our first release at the start of 2026.  

<br>  

  
### Things to note  

There are a number of key screens, journeys and parts of the feature that were not released as part of the first version. These include:  

- markdown editor not in place on the Weslh version page  
- make your form live pages don’t currently show any Welsh content
  - there is also a next step iteration for separate Welsh and English versions going live that were not part of this release    
- live read-only view screens not implemented  
- archive a Welsh version of your form not developed  
- group page not showing the forms with a Welsh version  

<br>  


## Designs for development  

### Create a form - task list  

<img alt="Create a form task list page. Screenshot" src="./screenshots-v2/001-create-a-form-new-form.png" width="500">  
Create a form task list page showing 1 of 9 tasks marked completed.

Through our round of testing we were able to confirm the introduction of a new optional section to provide a task to add Welsh translations alongside the English content added to a form. 

The new section content reads: 

> 4\. Create a Welsh version of your form (optional)  
> Add a Welsh version of your form 

The task link has a grey ‘optional’ tag to it’s right. This state will only appear if no Welsh has been added to the form.  


### Add a Welsh version of your form - new form without any content  

<img alt="Empty Add a Welsh version of your form page. Screenshot" src="./screenshots-v2/002-add-a-welsh-version-new-form.png" width="500">  

Add a Welsh version of your form page showing that no tasks have been completed in English yet.  

Under the page heading there’s a table component titled “Form name”. The table has 2 columns titled, “English content” and “Welsh content”.  
The next row shows the English form name added by the form creator on the left and a text input labelled “Enter your Welsh form name” on the right.  
This is a change from the original grey bordered tables from the tested iteration. We found that some form creators weren’t sure that they could change or add their translations into the boxes here, with one thinking they needed to go to aother page to add it. We believe that using the standard inputs on this screen will help make it obvious to form creators that this is the screen they should add translations to.  

After the form has just been completed the add a Welsh version of your form page can still be accessed. It just shows placeholder text at this point to say that nothing has been added yet.  

With a section headed for each part of their form, they read:

> Form questions  
> No questions have been added to the form yet.  
>   
> Declaration for people to agree to  
> No declaration was added to the form.  
>   
> Information about what happens next  
> No information about what happens next was added to the form yet.  
>   
> Payment link  
> No payment link has been added to the form.  
>   
> Link to privacy information for this form  
> No privacy information has been added to the form yet.  
>   
> Contact details for support  
> No contact details for support have been added to the form yet.  

The form creator can still technically save the page meaning a Welsh verison ‘exists’ even though all the inputs may be empty at this point. 


### Create a form - task list with Welsh version saved success banner  

<img alt="Create a form task list page with saved Welsh success banner. Screenshot" src="./screenshots-v2/003-create-a-form-created-welsh-version-without-any-content.png" width="500">  

Create a form task list page showing a green success banner, “The Welsh version of your form has been saved”.  
   
The optional Welsh task is now marked as ‘in progress’. This can be the case with or without content being added to the Welsh page until the Welsh version is deleted.  


### Deleting a Welsh version   

<img alt="Empty Add a Welsh version of your form page with delete button. Screenshot" src="./screenshots-v2/004-add-a-welsh-version-saved-welsh-version.png" width="500">  

Add a Welsh version of your form showing no English content yet but with a red “Delete Welsh version” button after the Welsh was ‘saved’.  

To remove the Welsh version, whether empty or full, the form creator can use the new ‘delete’ button.  

<br>  
  
<img alt="Are you sure you want to delete the Welsh version of your form page. Screenshot" src="./screenshots-v2/005-delete-welsh-version.png" width="500">  

Are you sure you want to delete the Welsh version of your form question page with ‘yes’ and ‘no’ radio options.  

<br>
  
<img alt="Create a form task list page with deleted Welsh success banner. Screenshot" src="./screenshots-v2/006-create-a-form-deleted-welsh-version.png" width="500">  

Create a form task list page showing a green success banner, “The Welsh version of your form has been deleted”.  

The optional Welsh task has been reset to show the grey ‘optional’ tag.  

  
### Add a Welsh version of your form - full English form created    

<img alt="Empty Add a Welsh version of your form page. Screenshot" src="./screenshots-v2/011-add-a-welsh-version-complete-form.png" width="500">  

Add a Welsh version of your form showing all the English tasks are complete with empty inputs for each bit of content.  

<br>  

  
<img alt="Completed Add a Welsh version of your form page. Screenshot" src="./screenshots-v2/011-add-a-welsh-version-complete-translations.png" width="500">  

Add a Welsh version of your form showing all the inputs have a Welsh translation and have been saved now showing a red “Delete Welsh version” button.  


  
### Create a form - saved and completed Welsh version    

<img alt="Create a form task list page showing Welsh version changes saved. Screenshot" src="./screenshots-v2/012-create-a-form-complete-form.png" width="500">  

Task list screen showing a green success banner, “The Welsh version of your form has been saved”.    

<br>   

  
<img alt="Create a form task list page showing Welsh version saved and marked completed. Screenshot" src="./screenshots-v2/012-create-a-form-complete-welsh.png" width="500">  

Task list screen showing a green success banner, “The Welsh version of your form has been saved and marked complete”.  
 
  
## Form filler screens 

### Previewing a form  
  
<img alt="First question page in a form with more complex guidance before the question. Screenshot" src="./screenshots-v2/1001-welsh-guidance-preview.png" width="500">  

First question page in a form with more complex guidance before the question and ‘Cymraeg’ language toggle selected.  
  
<br>  

  
<img alt="Date of birth question page in Welsh. Screenshot" src="./screenshots-v2/1002-welsh-date-preview.png" width="500">  

Date of birth question page in a form. The ‘Cymraeg’ language toggle is selected.  

<br>  

  
<img alt="Address question page in Welsh. Screenshot" src="./screenshots-v2/1003-welsh-address-preview.png" width="500">  

Address question page in a form. The ‘Cymraeg’ language toggle is selected.  

<br>  

  
<img alt="Check your answers before submitting page in Welsh. Screenshot" src="./screenshots-v2/1005-welsh-cya-open-email-preview.png" width="500">  

Check your answers before submitting page in a form. The ‘Cymraeg’ language toggle is selected.  

<br>  

<img alt="Your form has been submitted page in Welsh. Screenshot" src="./screenshots-v2/1006-welsh-confirmation-preview.png" width="500">  

Your form has been submitted page in a form. The ‘Cymraeg’ language toggle is selected.  

  
### Previewing a form with English content but no Welsh

<img alt="First question page in a form with guidance showing the English version. Screenshot" src="./screenshots-v2/009-previewing-form-without-welsh-translations-but-welsh-version-english.png" width="500">  
First question page in a form with guidance showing the English content and ‘English’ language toggle selected.  

<br>  
  
<img alt="First question page in a form with guidance showing English while the Welsh version toggle is selected. Screenshot" src="./screenshots-v2/009-previewing-form-without-welsh-translations-but-welsh-version-welsh.png" width="500">  
First question page in a form with guidance showing the English content while the ‘Welsh’ language toggle is selected.  

<br>  
  
___

<br>  
  
[Back to the top](#add-welsh-version-of-a-form-v2---first-release)
