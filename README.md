# AI PR Review

A language-agnostic GitHub Action that reviews every pull request with AI — real bugs, security issues, and clear simplification opportunities. It never blocks the merge; it's a reviewer's second pair of eyes, not a gate.

Bring your own key — **Anthropic, OpenAI, or Gemini**. Your code goes straight from the GitHub-hosted runner to the provider you choose. Nothing is proxied through a third-party server; this project runs no backend.

> **No Copilot-subscription option.** GitHub Models — the only path that would have let this action use a GitHub Copilot subscription instead of a separate API key — was fully retired by GitHub on July 30, 2026. There is currently no supported, ToS-compliant API for third-party tools to call Copilot for raw inference, so this action requires a direct API key from one of the three providers below.

## Providers

| `provider` | Credential needed | Notes |
|---|---|---|
| `anthropic` (default) | `anthropic-api-key` — [console.anthropic.com](https://console.anthropic.com/) | |
| `openai` | `openai-api-key` — [platform.openai.com](https://platform.openai.com/) | |
| `gemini` | `gemini-api-key` — [aistudio.google.com](https://aistudio.google.com/) | |

`model` is optional for every provider — if omitted, a reasonable current default is used, but provider model catalogs change often, so check the provider's docs if you want a specific one.

## Quick start

Add `.github/workflows/ai-pr-review.yml`:

```yaml
name: AI PR Review

on:
  pull_request:
    types: [opened, synchronize, reopened]

permissions:
  contents: read
  pull-requests: write

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: Njord-mx/ai-pr-review@v1
        with:
          anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
```

Add `ANTHROPIC_API_KEY` as a repository secret (Settings → Secrets and variables → Actions), and you're done. Works the same regardless of stack — see [`examples/`](./examples) for Go, Node/TypeScript, Python, .NET, and every provider.

**Important:** `fetch-depth: 0` on the checkout step is required — this action diffs against the PR's base branch, and a shallow clone won't have it.

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `provider` | no | `anthropic` | `anthropic`, `openai`, or `gemini` |
| `anthropic-api-key` | if provider is `anthropic` | — | Your Anthropic API key |
| `openai-api-key` | if provider is `openai` | — | Your OpenAI API key |
| `openai-base-url` | no | `https://api.openai.com/v1` | Base URL of any OpenAI-compatible API (an internal LLM gateway, Azure OpenAI behind a proxy, etc.), including the version path. Must be `https://`. Pass it from a secret if you don't want the URL in the workflow. |
| `gemini-api-key` | if provider is `gemini` | — | Your Gemini API key |
| `model` | no | provider-specific | Overrides the default model for the chosen provider |
| `max-diff-lines` | no | `2500` | Caps diff size sent to the model (cost/context guard) |
| `language` | no | `en` | Review comment language: `en` or `es` |
| `github-token` | no | `${{ github.token }}` | Token used to post the PR comment |

## How it works

1. Computes the PR's diff against its base branch.
2. Sends it to your chosen provider with a review-focused system prompt (bugs, security, simplification — not style).
3. Posts the findings as a PR comment, **updating the same comment** on every subsequent push instead of piling up duplicates.

## Cost

You pay your chosen provider directly for what you use — this project takes no cut and runs no infrastructure. A typical PR review costs a fraction of a cent to a few cents depending on diff size and model choice.

## License

MIT — see [LICENSE](./LICENSE).
