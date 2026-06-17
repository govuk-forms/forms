# ADR051: Add an exit page model

Date: 2026-06-17

## Status

Accepted

## Context

We are currently adding support for multiple branches and multiple routing conditions for each question to GOV.UK Forms. As part of this we also want to allow multiple exit pages per selection question, and multiple selection options for a selection question to go to the same exit page. In the proposed design exit pages are associated with one and only one (selection) question, and can only be routed to from that question.

Currently there is no separate model or database table for exit pages; instead the content for the exit page is part of the condition model:

```
# database schema

create_table :conditions do |t|
  ...
  t.text "exit_page_heading"
  t.text "exit_page_markdown"
end

# model

class Condition
  ...

  def is_exit_page?
    !exit_page_markdown.nil?
  end
end

# form document

{
  "steps": [
    {
      ...
      "routing_conditions": [
        {
           ...
           "exit_page_heading": ..., // string | null
           "exit_page_markdown": ... // string | null
        }
      ]
    }
  ]
}
```

This does not reflect the domain model that we present to users, and also does not allow routing to the same exit page more than once.

## Decision

We will add exit pages as separate objects to the form document. A form step which is a selection question will be able to have zero or more exit pages. Exit pages will have heading and markdown content. Conditions will have an optional reference to an exit page.

```
{
  "steps": [
    {
      "id": ...,
      ...
      "exit_pages" : [ // can be absent
        {
          "id": ...,
          "heading": ...,
          "markdown": ...,
          "question_page_id": ... // this is the same as the step id above
        }
        ...
      ],
      "routing_conditions": [
        {
           ...
           "exit_page_id": ..., // can be absent, or null if condition does not route to an exit page
                                // or if exit_page_heading and exit_page_markdown are not null
           "exit_page_heading": ..., // can be absent, or null if condition does not route to an exit page
                                     // or if exit_page_id is not null
           "exit_page_markdown": ... // can be absent, or null if condition does not route to an exit page
                                     // or if exit_page_id is not null
        }
      ]
    }
  ]
}
```

We will add an exit page model to forms-admin:

```
# database migration

create_table :exit_pages do |t|
  t.references :question_page, foreign_key: { to_table: :pages }, null: false # an exit page must be associated with a question

  t.text :heading
  t.text :markdown

  # plus timestamps, translations, etc...
end

add_reference :conditions, :exit_page, foreign_key: true, null: true # conditions can route to an exit page

# model changes

class Condition
  ...

  has_one :exit_page, optional: true

  def is_exit_page?
    !exit_page_id.nil? || !exit_page_markdown.nil?
  end
end

class ExitPage
  belongs_to :question_page, class_name: "Page", optional: false
  has_many :conditions

  # plus translations, validations, etc...
end
```


Conditions will still have `exit_page_heading` and `exit_page_markdown` to maintain backwards-compatibility, but these will be deprecated and may be removed in future.

## Consequences

- Exit pages will be modelled as seperate entities rather than attributes on conditions
- Multiple options for a question can route to the same exit page (but not multiple options from different questions)
- Consumers of form documents will have to look up an exit page in the list of exit pages for a question
- There will be a period where exit page content will be either in the condition or in an exit page record
