---
description: Implements the issue that just received the ai-task label and opens one pull request closing it.
on:
  issues:
    types: [labeled]

if: github.event.label.name == 'ai-task'

engine: claude
model: claude-sonnet-4.6

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

Quy trình tự động hóa phát triển phần mềm toàn diện cho Golang sử dụng pstack global plugin và Claude Code thông qua `gh aw`.

## Triggers
- Khi một GitHub Issue được gán nhãn `ai-task`.
- Khi có Pull Request mới được mở hoặc đồng bộ (`pull_request`).

## Steps / Instructions for the Agent:

1. **Environment Setup & Global Pstack Installation:**
   - Clone repository.
   - Thiết lập môi trường Golang (`actions/setup-go@v5`).
   - Cài đặt marketplace và plugin pstack toàn cục cho Claude Code trên môi trường CI:
     ```bash
     /plugin marketplace add cursor/plugins
     /plugin install pstack@cursor-plugins
     ```

2. **Route via /pstack:poteto-mode (nếu kích hoạt từ Issue):**
   - Đọc nội dung GitHub Issue.
   - Khởi chạy Claude Code CLI theo triết lý của pstack [`/poteto-mode`](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/02-poteto-mode.md): *"repro first, then fix, verify, and open a PR"*.
   - Thực thi trong một `worktree` sạch sẽ để tránh xung đột mã nguồn.

3. **Verify and Prove It Works (Golang & Pstack Verification):**
   - Chạy kiểm thử Go kèm race detector: `go test -v -race ./...` để chứng minh mã chạy đúng (`Prove It Works`).
   - Nếu cần kiểm chứng giao diện hoặc kịch bản phức tạp, gọi kỹ năng kiểm thử của pstack (`/pstack:create-verification-skill` nếu chưa có) theo [06-verify-and-ship.md](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/06-verify-and-ship.md) để hệ thống tự động lái và xác thực ứng dụng.

4. **Babysit & PR Management:**
   - Tự động mở Pull Request kèm theo log bằng chứng thực tế.
   - Nếu CI/CD báo lỗi biên dịch hoặc unit test fail, agent tự động đọc stack trace, vá lỗi bằng Go idiom sạch sẽ và push lại (vòng lặp Babysit).
