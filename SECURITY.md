# Security Policy

## How this action handles your code

- Your PR diff is sent directly from the GitHub-hosted runner to the provider you choose (Anthropic, OpenAI, Google, or GitHub Models), using **your own** credential. It never passes through any server operated by this project — there is no backend.
- Nothing is logged, stored, or retained by this project.
- The only credentials this action needs are the provider key/token you configure (e.g. `ANTHROPIC_API_KEY` as a repository secret, or nothing extra for `github-models`, which uses the workflow's own token), plus the default `github.token` (scoped to the calling repo) to post PR comments.

## Reporting a vulnerability

Please open a private security advisory on this repository (Security tab → "Report a vulnerability") rather than a public issue. We'll acknowledge reports within 5 business days.
