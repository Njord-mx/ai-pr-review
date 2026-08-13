# AI PR Review

A language-agnostic GitHub Action that reviews every pull request with AI — real bugs, security issues, and clear simplification opportunities. It never blocks the merge; it's a reviewer's second pair of eyes, not a gate.

Bring your own key — **Anthropic, OpenAI, or Gemini** — or use your **GitHub Copilot subscription** via GitHub Models. Your code goes straight from the GitHub-hosted runner to the provider you choose. Nothing is proxied through a third-party server; this project runs no backend.

## Providers

| `provider` | Credential needed | Notes |
|---|---|---|
| `anthropic` (default) | `anthropic-api-key` — [console.anthropic.com](https://console.anthropic.com/) | |
| `openai` | `openai-api-key` — [platform.openai.com](https://platform.openai.com/) | |
| `gemini` | `gemini-api-key` — [aistudio.google.com](https://aistudio.google.com/) | |
| `github-models` | Nothing extra — uses the workflow's own token by default | Uses your **GitHub Copilot subscription** through [GitHub Models](https://docs.github.com/en/github-models). Requires `models: read` permission on the job and GitHub Models enabled for your account/org — rate limits scale with your Copilot plan. If your org blocks the default token from calling Models, pass a personal access token via `github-models-token` instead. |

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

### Using GitHub Copilot instead of an API key

```yaml
permissions:
  contents: read
  pull-requests: write
  models: read

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: Njord-mx/ai-pr-review@v1
        with:
          provider: github-models
```

No API key to manage — this calls GitHub Models under the hood, and your Copilot plan determines the rate limit.

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `provider` | no | `anthropic` | `anthropic`, `openai`, `gemini`, or `github-models` |
| `anthropic-api-key` | if provider is `anthropic` | — | Your Anthropic API key |
| `openai-api-key` | if provider is `openai` | — | Your OpenAI API key |
| `gemini-api-key` | if provider is `gemini` | — | Your Gemini API key |
| `github-models-token` | no | `${{ github.token }}` | Token for GitHub Models, only used when `provider: github-models` |
| `model` | no | provider-specific | Overrides the default model for the chosen provider |
| `max-diff-lines` | no | `2500` | Caps diff size sent to the model (cost/context guard) |
| `language` | no | `en` | Review comment language: `en` or `es` |
| `github-token` | no | `${{ github.token }}` | Token used to post the PR comment (separate from `github-models-token`) |

## How it works

1. Computes the PR's diff against its base branch.
2. Sends it to your chosen provider with a review-focused system prompt (bugs, security, simplification — not style).
3. Posts the findings as a PR comment, **updating the same comment** on every subsequent push instead of piling up duplicates.

## Cost

You pay your chosen provider directly for what you use (or nothing extra, if you're already paying for Copilot and use `github-models`) — this project takes no cut and runs no infrastructure. A typical PR review costs a fraction of a cent to a few cents depending on diff size and model choice.

## License

MIT — see [LICENSE](./LICENSE).
