---
description: Implements the issue that just received the ai-task label and opens one pull request closing it.
on:
  issues:
    types: [labeled]

if: github.event.label.name == 'ai-task'

engine: claude
model: claude-sonnet-4.6
# Khai báo skill pstack
skills:
  - cursor/plugins/pstack@main

timeout-minutes: 30

permissions:
  contents: read
  issues: read

tools:
  edit:
  bash:
    - go:*
    - gofmt:*
    - git status
    - git diff
    - git log

network:
  allowed:
    - defaults
    - go

safe-outputs:
  create-pull-request:
    max: 1
    normalize-closing-keywords: true
  add-comment:
    max: 1

---

# Task implementation

# Agentic Full SDLC Workflow for Go (pstack & Claude Code)

Quy trình tự động hóa phát triển phần mềm toàn diện cho Golang sử dụng pstack global plugin và Claude Code thông qua `gh aw`. Luon luon su dung potato-mode cua pstack khi lam task.

## Triggers
- Khi một GitHub Issue được gán nhãn `ai-task`.
- Khi có Pull Request mới được mở hoặc đồng bộ (`pull_request`).

## Steps / Instructions for the Agent:

Use potato-mode to implement issue.