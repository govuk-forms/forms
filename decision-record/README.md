# Decision Records

As recorded in [DR000-use-github-and-the-adr-process.md](DR000-use-github-and-the-adr-process.md) we are using the [ADR process](../ADR/) to record significant decisions for GOV.UK Forms in addition to architectural ones.

Proposing and reviewing decisions requires an understanding of the GitHub and [pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests). 

## Proposing a new decision

1. Add a new file with the naming convention `DRXXX-short-decision-description.md` where `XXX` is the highest existing DR number + 1
2. Use the [decision record Markdown template](DRXXX-decision-record-template.md) for the structure, "Raw" allows you to view the Markdown
3. Set the Status to "Proposed"
4. Set the branch name to match the new filename
5. Propose the new file and start the pull request process

## Reviewing a decision

1. Find the decision record in the list of [pull requests](https://github.com/govuk-forms/forms/pulls)
2. Add a comment and / or approve the pull request

## Approving / superseding / rejecting a decision

1. Set the Status to either
   - "Approved" (and where appropriate set the status of any superceded decision to "Superseded by DRXXX"), or
   - "Rejected" 
3. Merge the pull request
