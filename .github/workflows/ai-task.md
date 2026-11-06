---
description: Implements the issue that just received the ai-task label and opens one pull request closing it.
on:
  issues:
    types: [labeled]

if: github.event.label.name == 'ai-task'

engine: codex
model: openai/gpt-5.6-sol

# Khai báo skill pstack
skills:
  - cursor/plugins/pstack@main

timeout-minutes: 30

permissions:
  contents: read
  issues: read

tools:
  edit:
  # Engine codex khong ho tro bash command allow-list (bi ignore khi runtime).
  # Dung ["*"] de cap phep bash; sandbox cua codex van giu ranh gioi.
  bash: ["*"]

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

Quy trình tự động hóa phát triển phần mềm toàn diện cho Golang (Agentic Full SDLC) sử dụng pstack global plugin thông qua `gh aw`. Luon luon su dung potato-mode cua pstack khi lam task.

## Triggers

- Khi một GitHub Issue được gán nhãn `ai-task`.

## Steps / Instructions for the Agent

Use potato-mode to implement issue.
