# PR-Reviewer-Test

This is the sandbox repository for my n8n-powered PR Auto-Reviewer bot. Its only purpose is to give the bot real pull requests to review. Open a PR here and the bot will post a structured review comment automatically.

The main project repo with full setup docs: [pr-auto-reviewer](https://github.com/yashgoyal5571/pr-auto-reviewer)

---
## How to test the bot yourself
1. Fork this repository.
2. Make a small change to any file (or create a new one).
3. Open a Pull Request against this repo.
4. Wait a few seconds for the bot to post its review.


## What the bot checks

| Check | Details |
|---|---|
| Title format | Validates against Conventional Commits — feat, fix, docs, chore, refactor, perf, ci, build, style, test |
| PR size | Classifies as Small, Medium, Large, or Very Large based on total line changes |
| Files changed | Full per-file breakdown with status, additions, and deletions |
| File types | Lists every unique extension present in the PR |
| Sensitive files | Flags package.json, package-lock.json, yarn.lock, .env variants, Dockerfile, docker-compose.yml, requirements.txt, and CI/CD configs |

---

## Example bot output

The comment below was posted automatically on [PR #26](https://github.com/yashgoyal5571/pr-reviewer-test/pull/26).

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
Last updated: 2026-06-26 20:22
```

The bot also sends a color-coded Discord embed on every PR event. Red if sensitive files are touched, green for small clean PRs, yellow and orange as size increases.

---

## Architecture

```
GitHub PR opened or synchronize event
              |
         Webhook (n8n)
              |
    Fetch PR Files via GitHub REST API
    (uses repository.full_name — fork-safe)
              |
      Analyze PR and build review content
              |
   Fetch existing PR comments from GitHub API
              |
   Scan comments for hidden bot marker
              |
    POST new comment  or  PATCH existing one
              |
     Notify Discord via color-coded embed
     (Continue On Fail enabled — GitHub comment
      posts even if Discord is unavailable)
```

The bot uses an idempotent comment pattern. It posts once on PR open, then patches the same comment on every subsequent push. No duplicate comments across multiple commits to the same PR.

---

## Tech stack

- n8n — workflow orchestration, self-hosted via Docker Desktop
- ngrok — exposes local n8n instance to GitHub webhooks
- GitHub Webhooks and REST API — event trigger, file fetch, and comment write
- Discord Webhooks — color-coded embed notifications
- Docker environment variables — secrets never hardcoded or exported in workflow JSON

---

## Note on availability

The bot runs locally on Docker Desktop. It is active while my machine is on. This is intentional for a portfolio project at this stage. Production deployment on a VPS is the planned next step.

---

## 🚀 Future Roadmap & Scalability
This project currently runs on a local Docker environment, which is perfect for development, testing, and understanding the core webhook orchestration logic.

* **Current State**: Local-first development environment. The bot is active while the host machine is running.
* **Production Roadmap**: 
    * [ ] Migration to a persistent cloud VPS (e.g., Oracle Cloud Always Free or similar) for 24/7 uptime.
    * [ ] Implementation of organization-level GitHub App authentication to replace Personal Access Tokens (PATs).
    * [ ] Database integration for long-term analytics on PR trends and code-review metrics.

I am treating this as a living, iterative project. If you are interested in sponsoring the cloud hosting or contributing to the production migration, please reach out via GitHub issues!

---

Built while learning API orchestration and backend automation with n8n.
