# actions-automerge

Reusable GitHub Actions workflow that enables native auto-merge (rebase) on Dependabot
pull requests, gated by update type: patch and minor updates merge themselves once required
checks pass; major updates stay open for human review. There is no native auto-merge in
`dependabot.yml` — this workflow is the missing piece, built on
[dependabot/fetch-metadata](https://github.com/dependabot/fetch-metadata).

Part of a fleet of independently versioned reusable workflows (`actions-*` repos on this
account), released by
[actions-release](https://github.com/RobinCombrink/actions-release).

## Usage

`.github/workflows/automerge.yml` in the calling repo:

```yaml
name: automerge

on:
  pull_request:

permissions:
  contents: write
  pull-requests: write

jobs:
  automerge:
    uses: RobinCombrink/actions-automerge/.github/workflows/automerge.yml@<commit-sha> # vX.Y.Z
```

Pin the exact commit SHA of the release you want (the `# vX.Y.Z` comment keeps it readable);
Dependabot bumps SHA and comment together.

To widen or narrow what auto-merges:

```yaml
    with:
      max-update-type: patch # or major
```

## Requirements

- **Auto-merge only arms when merging is blocked** — the repo needs a ruleset with required
  status checks (and "Allow auto-merge" enabled in settings). Without required checks the
  PR is immediately mergeable and GitHub refuses to arm auto-merge.
- The PR merges only after all required checks pass; a red check means the PR just sits
  there, which is the correct failure mode.
- Pairs with a Dependabot `cooldown` so updates age before they're even proposed.
