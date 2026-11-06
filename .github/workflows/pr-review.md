---
on:
  pull_request:
    types: [labeled]

engine: claude
model: claude-haiku-4.5

permissions:
  contents: read
  pull-requests: read

safe-outputs:
  add-comment:
    max: 1
  create-pull-request-review-comment:
    max: 10
  submit-pull-request-review:
    allowed-events: [COMMENT]

---

# Pull Request Review Assistant


Post a message ("Received") on issue.