# Security Policy

## How this action handles your code

- Your PR diff is sent directly from the GitHub-hosted runner to the provider you choose (Anthropic, OpenAI, or Google), using **your own** credential. It never passes through any server operated by this project — there is no backend.
- Nothing is logged, stored, or retained by this project.
- The only credentials this action needs are the provider API key you configure as a repository secret, plus the default `github.token` (scoped to the calling repo) to post PR comments.

## Reporting a vulnerability

Please open a private security advisory on this repository (Security tab → "Report a vulnerability") rather than a public issue. We'll acknowledge reports within 5 business days.
