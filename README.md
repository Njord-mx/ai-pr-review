# AI PR Review

A language-agnostic GitHub Action that reviews every pull request with Claude — real bugs, security issues, and clear simplification opportunities. It never blocks the merge; it's a reviewer's second pair of eyes, not a gate.

Bring your own [Anthropic API key](https://console.anthropic.com/) — your code goes straight from the GitHub-hosted runner to the Anthropic API you control. Nothing is proxied through a third-party server.

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

Add `ANTHROPIC_API_KEY` as a repository secret (Settings → Secrets and variables → Actions), and you're done. Works the same regardless of stack — see [`examples/`](./examples) for Go, Node/TypeScript, Python, and .NET.

**Important:** `fetch-depth: 0` on the checkout step is required — this action diffs against the PR's base branch, and a shallow clone won't have it.

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `anthropic-api-key` | yes | — | Your Anthropic API key |
| `model` | no | `claude-sonnet-5` | Claude model to use |
| `max-diff-lines` | no | `2500` | Caps diff size sent to the model (cost/context guard) |
| `language` | no | `en` | Review comment language: `en` or `es` |
| `github-token` | no | `${{ github.token }}` | Token used to post the PR comment |

## How it works

1. Computes the PR's diff against its base branch.
2. Sends it to Claude with a review-focused system prompt (bugs, security, simplification — not style).
3. Posts the findings as a PR comment, **updating the same comment** on every subsequent push instead of piling up duplicates.

## Cost

You pay Anthropic directly for what you use — this project takes no cut and runs no infrastructure. A typical PR review costs a fraction of a cent to a few cents depending on diff size and model choice.

## License

MIT — see [LICENSE](./LICENSE).
