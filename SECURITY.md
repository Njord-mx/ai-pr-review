# Security Policy

## How this action handles your code

- Your PR diff is sent directly from the GitHub-hosted runner to `api.anthropic.com`, using **your own** Anthropic API key. It never passes through any server operated by this project.
- Nothing is logged, stored, or retained by this project — there is no backend.
- The only credential this action needs is the `ANTHROPIC_API_KEY` you provide as a repository secret, plus the default `github.token` (scoped to the calling repo) to post PR comments.

## Reporting a vulnerability

Please open a private security advisory on this repository (Security tab → "Report a vulnerability") rather than a public issue. We'll acknowledge reports within 5 business days.
