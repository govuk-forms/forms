# ADRxxx: Add support for more comparisons to conditions

Date: 2026-06-11

## Status

Accepted

## Context

> the facts behind the need to make the decision

We are currently adding support for multiple branches and multiple routing conditions for each question to GOV.UK Forms. As part of this we also want to allow multiple exit pages per selection question, and multiple selection options for a selection question to go to the same exit page.

While the current structure for form documents allows for multiple conditions per question, it only allows for a condition to be triggered by one possible selection option of a selection question:

```
# example of a form with a question with multiple conditions, in the current form document schema
# some properties omitted for brevity

{
  "name": "A long form with branches",
  "steps": [
    {
      "id": "JJqRJCYs",
      "data": {
        "hint_text": null,
        "answer_type": "selection",
        "is_optional": false,
        "page_heading": null,
        "is_repeatable": false,
        "question_text": "Where will you be studying?",
        "answer_settings": {
          "only_one_option": "true",
          "selection_options": [
            {
              "name": "England",
              "value": "England"
            },
            {
              "name": "Wales",
              "value": "Wales"
            },
            {
              "name": "Northern Ireland",
              "value": "Northern Ireland"
            },
            {
              "name": "Scotland",
              "value": "Scotland"
            }
          ]
        },
        "guidance_markdown": null
      },
      "type": "question",
      "position": 3,
      "next_step_id": "WHoH7UAZ",
      "routing_conditions": [
        {
          ...,
          "skip_to_end": false,
          "answer_value": "England",
          "goto_page_id": "9GRmRKY4",
          "check_page_id": "JJqRJCYs",
          "routing_page_id": "JJqRJCYs",
          "exit_page_heading": null,
          "validation_errors": [],
          "exit_page_markdown": null
        },
        {
          ...,
          "skip_to_end": false,
          "answer_value": "Wales",
          "goto_page_id": "9GRmRKY4",
          "check_page_id": "JJqRJCYs",
          "routing_page_id": "JJqRJCYs",
          "exit_page_heading": null,
          "validation_errors": [],
          "exit_page_markdown": null
        },
        {
          ...,
          "skip_to_end": false,
          "answer_value": "Northern Ireland",
          "goto_page_id": "9GRmRKY4",
          "check_page_id": "JJqRJCYs",
          "routing_page_id": "JJqRJCYs",
          "exit_page_heading": null,
          "validation_errors": [],
          "exit_page_markdown": null
        }
      ]
    },
    ...
  ],
  ...
}
```

This limitation is a particular problem for exit pages, where we want to avoid duplicating exit page content.

## Decision

After exploring different options, we have decided to extend conditions so that one condition can be triggered by multiple different possible selection options for a selection question.

We have decided to do this by expanding the form document schema for conditions so that `answer_value` can be a JSON object which describes a comparison:

```
# example of a form with a question with a condition that triggers on multiple answer values, in the proposed form document schema
# some properties omitted for brevity

{
  "name": "A long form with branches",
  "steps": [
    {
      "id": "JJqRJCYs",
      "data": {
        "hint_text": null,
        "answer_type": "selection",
        "is_optional": false,
        "page_heading": null,
        "is_repeatable": false,
        "question_text": "Where will you be studying?",
        "answer_settings": {
          "only_one_option": "true",
          "selection_options": [
            {
              "name": "England",
              "value": "England"
            },
            {
              "name": "Wales",
              "value": "Wales"
            },
            {
              "name": "Northern Ireland",
              "value": "Northern Ireland"
            },
            {
              "name": "Scotland",
              "value": "Scotland"
            }
          ]
        },
        "guidance_markdown": null
      },
      "type": "question",
      "position": 3,
      "next_step_id": "WHoH7UAZ",
      "routing_conditions": [
        {
          ...,
          "skip_to_end": false,
          "goto_page_id": "9GRmRKY4",
          "check_page_id": "JJqRJCYs",
          "routing_page_id": "JJqRJCYs",
          "exit_page_heading": null,
          "exit_page_markdown": null,
          "validation_errors": [],
          "answer_value": {
            "operator": "in",
            "values": ["England", "Scotland", "Wales"],
          }
        }
      ]
    },
    ...
  ],
  ...
}
```


The structure chosen to describe the comparison looks generically like this:

```
{
  "answer_value": {
    "operator": ..., # string
    ... # other propertie as required for the operator
  }
}
```

This can in future support arbitraty operators, but for this ADR we define only one operator, "in", which checks whether the answer is included in the array `values`.

```
  "answer_value": {
    "operator": "in",
    "values": [..., ...], # array of strings
  }
```

As well as being forwards-compatible with future possible comparisons, this change is backwards-compatible with the existing form document schema.

Alongside `answer_value: nil`, and `answer_value: ... # String`, adding `answer_value: { operator: "in", values: ... }` gives us three different possible comparisons (any, is, and in). We could consider in a future ADR introducing a backwards-incompatible change to the ADR to rationalise all these comparisons to use the same structure, if we wished.

## Consequences

Positive consequences:
- We can allow multiple selection options from one selection question to go to the same exit page
- We can reduce the size of form documents with selection questions that have multiple conditions routing from and going to the same question
- The form document schema changes are both backwards- and forwards-compatible

Negative consequences:
- The code for creating and updating conditions in forms-admin may be more complex
- There is ambiguity in the chosen schema, there are now multiple ways to represent the same condition

Additionally, as part of implementing this change, we will either change the column `answer_value` in the `conditions` table in the forms-admin database to be a `jsonb` type, or add a new `answer_value_json` column to that table, to hold the comparison JSON.
