# The Scan: daily find-and-update workflow

This list keeps itself current. **The Scan** is a daily [Claude Code](https://docs.anthropic.com/en/docs/claude-code) workflow that finds new work on agents that act in the physical world, vets it against [CONTRIBUTING.md](CONTRIBUTING.md), verifies every number against the live page, and opens a pull request with the additions. A maintainer reviews and merges that pull request; the bot cannot. It is ported from the scan in [benchflow-ai/awesome-evals](https://github.com/benchflow-ai/awesome-evals) and adapted to robotics sources and the embodied bar.

## What it does

1. **Read** `README.md`, `CONTRIBUTING.md` and this file; collect every URL and title already listed as the dedup set (a paper, its project page, its repo and its model card count as one work).
2. **Discover**: one parallel sub-agent per source group in [`.scanner/seeds.json`](.scanner/seeds.json), fetching feeds first and backing up JavaScript-heavy pages with WebSearch recency queries.
3. **Vet** each candidate in its own sub-agent against the bar below. *When in doubt, leave it out* licenses rejecting a weak candidate, never skipping the search that would have found a strong one.
4. **Verify** survivors with skeptic sub-agents that re-fetch the live page; a number goes in only if it is a verbatim quote from one sentence of the source.
5. **Integrate** each survivor into its section of `README.md` in the house entry format.
6. **Open one pull request** on a `scan/<date>` branch (plain commit message, no AI attribution) that quotes the evidence for each addition, or open nothing and report "no new items".
7. **Report** every source fetched and every candidate rejected, so a zero-find day can be told apart from a skipped search. The workflow keeps that report and the tool-call log (tool names and inputs only, never tool output) as a run artifact for 14 days.

## Sources

[`.scanner/seeds.json`](.scanner/seeds.json) is the source registry; adding a source is a one-line edit. Every URL in it returned HTTP 200 on 2026-10-20.

| Group | What it covers |
|---|---|
| `lab-blogs` | Google DeepMind and the Google Developers blog, NVIDIA (blog, developer blog, GEAR), Physical Intelligence, Anthropic research, OpenAI news, Hugging Face, Ai2, Meta AI, Dimensional, Robocurve, Andon Labs, Dream Labs |
| `authors` | Phillip Isola, Chris Paxton (It Can Think), Eric Jang |
| `arxiv` | the cs.RO daily feed, and an API query for agents, coding agents, agent-as-policy, world-action models and benchmarks |
| `hf` | Hugging Face daily papers (robotics only) and trending robotics models |
| `github` | `gh api` searches for new robotics, embodied-AI and robot-MCP repos, and releases of the watched repos behind listed entries |
| `podcasts-talks` | Latent Space (robotics episodes, a feed awesome-evals also uses), plus searches for new talks |
| `open-web` | WebSearch queries for launches: agent as a policy, robot-use agents, world-action model APIs, robot leaderboards, robot MCP servers |

Of awesome-evals' own sources, the scan reuses the ones that cover robotics: Anthropic research, OpenAI news, Latent Space, Hugging Face papers and the arXiv API. The rest (eval-practitioner blogs) rarely publish on robots, so they are left out.

## The bar

The bar is [CONTRIBUTING.md](CONTRIBUTING.md). On top of awesome-evals' rules (show your work, a one-line why, verified URLs, quotable numbers, evidence that someone else uses a tool, disclosed affiliation), an embodied entry must answer:

- **Is there something to run or read?** Code, weights, a public API, data, a paper or a reproducible evaluation. A demo video, a social post or a funding round with no artifact is not an entry.
- **Who judged success?** The simulator's state, a held-out checker, a human rater, or the agent itself; how many trials; simulation or a real robot. Self-reported, single-trial or agent-judged results get ⚠️.
- **What is the licence?** Non-commercial, gated, missing or "coming soon" code, weights, data or assets get ⚠️.

A typical day adds 0 to 3 items. Zero is a legitimate result only when the sources were actually fetched.

## Output: a pull request a human must approve

The scan works on a `scan/<date>` branch and opens one pull request against `main`. It cannot merge, approve or push to `main`: the permission file [`.github/claude-scan-settings.json`](.github/claude-scan-settings.json) denies merges, reviews, force pushes and pushes to `main`, and the ruleset below makes approval a GitHub rule rather than an instruction. On a day with nothing new it changes nothing.

The scan state lives in `.scanner/` and changes only inside an approved scan pull request: `state.json` holds the date of the last scan pull request (the floor for the next search, with a 14-day cap and 3 days of overlap), and `rejected.json` holds candidates rejected on their merits, so they are not proposed again.

A second workflow, [`respond.yml`](.github/workflows/respond.yml), comments on new issues, new pull requests from people (never the bot's own) and `@claude` mentions. It reviews submissions against the bar and can only comment.

## What the founder must set up before it runs

The workflows hold no secret values; they read named GitHub secrets.

| Secret | Required | What it is |
|---|---|---|
| `CLAUDE_CODE_OAUTH_TOKEN` | yes | A Claude subscription OAuth token, from `claude setup-token`. Both workflows use it. |
| `SLACK_BOT_TOKEN` | no | A Slack bot token with `chat:write`, invited to the channel. Slack steps stay off unless both Slack secrets are set. |
| `SLACK_CHANNEL_ID` | no | The ID of the channel that gets the start message and the threaded result. |

`GITHUB_TOKEN` is provided by Actions; nothing to add.

Repository settings:

1. **Settings → Actions → General → Workflow permissions:** choose "Read and write permissions" and tick "Allow GitHub Actions to create and approve pull requests". Without the tick, the scan cannot open its pull request.
2. **Settings → Rules → Rulesets → New branch ruleset** targeting `main`: require a pull request before merging with at least 1 approval, dismiss stale approvals on new commits, block force pushes, restrict deletions, and leave the bypass list empty. Optionally add a `CODEOWNERS` file naming the maintainers and require code-owner review, so an approval from a bot never counts.
3. Make sure Actions is enabled for the repository. Scheduled workflows run only from the default branch, and GitHub pauses a schedule after 60 days without repository activity; re-enable it from the Actions tab if that happens.

Then:

```bash
claude setup-token                                                     # prints a subscription token
gh secret set CLAUDE_CODE_OAUTH_TOKEN -R benchflow-ai/awesome-embodied-agents
gh workflow run "Embodied Scan (find + update)" -R benchflow-ai/awesome-embodied-agents
#   optional:  -f focus=arxiv   or   -f lookback_days=60   for a first, wider sweep
```

Notes:

- The schedule is daily at 14:17 UTC, an hour after awesome-evals' scan, so two scans on one subscription do not overlap. Edit the `cron` line to change it.
- Both workflows pin the model in `claude_args` (`claude-sonnet-4-6`, as in awesome-evals); change it there.
- Pull requests opened with `GITHUB_TOKEN` do not start other workflows, so the responder never reviews the scan's own pull requests, and no other checks run on them.

## Dogfood it locally

Run the same loop on your machine before trusting the cloud. It needs a checkout of this repository, `gh` signed in, and Claude Code. The extra instruction at the end keeps the local run from pushing: it prints the diff and the search report instead.

```bash
sed -n '/You are the autonomous daily curator/,/curation, not volume/p' .github/workflows/embodied-scan.yml \
  | sed -e 's/^ \{12\}//' -e 's/\${{ github.event.inputs.focus }}//' \
        -e "s/\${{ github.event.inputs.lookback_days || '14' }}/14/" > /tmp/scan-prompt.md
claude -p --settings .github/claude-scan-settings.json \
  "$(cat /tmp/scan-prompt.md) This is a local dry run: do not create a branch, commit, push or open a pull request; print the README diff you would make and the search report."
```

Read the diff and the search report before letting the cloud version run unattended.
