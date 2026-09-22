# PR-Reviewer-Test

Sandbox repository for the [PR Auto-Reviewer](https://github.com/yashgoyal5571/pr-auto-reviewer) bot. Open a PR here and the bot posts a structured review comment automatically.

> **⚠️ Note on Demo Status:** This bot runs on a self-hosted n8n instance. If it doesn't comment immediately, it means the hosting environment is currently offline. For 24/7 reliability and full deployment options, please refer to the [main repository](https://github.com/yashgoyal5571/pr-auto-reviewer).

---
## How to test

1. Fork this repository
2. Make any change to any file, or create a new one
3. Open a pull request against this repo
4. The bot comments within a few seconds

---

## What the bot checks

| Check | Details |
|---|---|
| Title format | Validates against Conventional Commits: feat, fix, docs, chore, refactor, perf, ci, build, style, test |
| PR size | Small, Medium, Large, or Very Large based on total line changes |
| Files changed | Per-file breakdown with status, additions, and deletions |
| File types | All unique extensions in the PR |
| Sensitive files | Flags .env variants, Dockerfile, docker-compose.yml, package.json, package-lock.json, yarn.lock, requirements.txt, CI/CD configs |

---

## Example bot output

```
🤖 PR Auto-Review

Title Format: ✅ Follows conventional commit format

Size: 🟢 Small (34 lines)
Changes:
- `dockerfile` (modified): +15/-0
- `package.json` (modified): +4/-9
- `test.md` (modified): +6/-0
Files Changed: 3
File Types: (no ext), .json, .md

⚠️ Sensitive files modified:
- package.json

---
Posted by your n8n PR Auto-Reviewer
Last updated: 2026-06-28 16:51
```

The bot also fires notifications on Slack, Discord, and Telegram depending on which platforms are enabled. All three can run simultaneously or individually — controlled by boolean flags in a single node.

---

## Architecture

```
GitHub PR opened or synchronize event
              |
  Github Webhook (PR Events) (n8n)
         Raw Body enabled
              |
      Verify Signature
   HMAC-SHA256 — rejects spoofed payloads
              |
     Set Notification Flags
   Sets per-platform boolean flags
              |
    Fetch PR Files via GitHub REST API
    Paginated — all files regardless of count
              |
         Analyze PR
   Builds GitHub comment, Discord embed,
   Slack Block Kit, Telegram Rich HTML
              |
   Fetch existing PR comments (paginated)
              |
   Scan for hidden bot marker
              |
    POST new comment or PATCH existing one
              |
   Route Enabled Platforms — All Matching mode
   Fires every enabled platform in parallel
      |            |            |
  Discord       Slack       Telegram
```

The bot uses an idempotent comment pattern. It posts once on PR open, then patches the same comment on every subsequent push. No duplicate comments.

---

## Tech stack

- n8n — workflow orchestration, self-hosted via Docker
- GitHub Webhooks and REST API — event trigger, paginated file fetch, comment write
- HMAC-SHA256 signature verification — rejects forged webhook payloads
- Discord Webhooks — color-coded embed notifications
- Slack API — Block Kit attachment via chat.postMessage
- Telegram Bot API 10.1 — Rich Message via sendRichMessage
- Docker environment variables — secrets never hardcoded in exported workflow JSON

---

## Note on availability

The bot runs on a self-hosted n8n instance. It is active while that instance is running. See [DEPLOYMENT.md](https://github.com/yashgoyal5571/pr-auto-reviewer/blob/main/DEPLOYMENT.md) in the main repo for hosting options including VPS and n8n Cloud.

---

Built while learning API orchestration and backend automation with n8n.
