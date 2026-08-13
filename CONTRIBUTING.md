# Contributing

Issues and PRs welcome. A few ground rules:

- Keep the action language-agnostic — it should never assume a specific stack, build tool, or file layout.
- No new runtime dependencies beyond `bash`, `curl`, `jq`, and the `gh` CLI (all preinstalled on GitHub-hosted runners) — this keeps the action fast to start and easy to audit.
- Test changes against a real PR in a scratch repo before opening one here; there's no CI harness for the action's own behavior yet.
