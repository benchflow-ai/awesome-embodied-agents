# Contributing to Awesome Embodied Agents

Thanks for helping keep this an annotated, verified map of agents that act in the physical world.

## What belongs here

Resources about agents that sense and act in the physical world, with an emphasis on coding agents and LLMs used as robot policies ("agent as a policy"): harnesses that connect agents to robots, robot reasoning models and vision-language-action models (VLAs), world models, benchmarks and evaluations, simulators and asset libraries, perception tools, datasets, talks, and the landscape. A robotics resource with no agent angle of its own belongs when agents, or the evaluation of agents, depend on it: a standard benchmark suite, a simulator, an asset library, a perception model that agents call as a tool.

## Bar for inclusion

- **Show your work.** Code, weights, a public API, data, a paper or a reproducible evaluation. A demo video, a social post or a funding announcement with nothing to run or read is not an entry yet.
- **One-line why.** Every entry says what it is and why it belongs in its section.
- **Say who judged success.** For any robot result, the entry or the PR says whether success came from the simulator's state, a held-out checker, a human rater or the agent itself, how many trials ran, and whether it was in simulation or on a real robot. Self-reported, single-trial or agent-judged results get a ⚠️.
- **Say what the licence allows.** Non-commercial, research-only, gated or missing licences on code, weights, data or assets get a ⚠️. Robot evaluations often inherit scene and asset licences that are stricter than the code's.
- **"Coming soon" is a caveat.** If the code, weights or data are announced but not released, say so with a ⚠️.
- **Verify the URL.** Use the canonical repo, paper or project page, not a mirror or an aggregator. Check for moved or archived repos.
- **Prune the dead.** Archived, abandoned or withdrawn projects are removed or clearly flagged, never silently listed.
- **Right section.** Match the section's theme; cross-list only when it is genuinely warranted.
- **Tools need evidence someone else uses them.** Stars, external issues or pull requests, adoption by another project, or a third-party write-up. A brand-new repo with no users outside the authoring org is a "come back later", not a listing. Launches by established labs with a public paper, API or weights are the exception.
- **Disclose affiliation.** If you wrote or maintain the resource, say so in the PR body. BenchFlow maintains this list and builds Robo Use, which is listed; BenchFlow projects are held to the same bar.
- **Every number must be quotable.** If an annotation cites a figure, it appears verbatim in the linked source. Numbers that live only on a moving leaderboard are dated or dropped.
- **Safety claims need a protocol.** A claim that a robot, model or policy is safe or unsafe cites what was measured, by whom, and over how many trials.

## Format

```
- **[Title](https://url)** — Author/Org — <https://url> · *type, YYYY-MM-DD* — what it is and why it belongs. 🆕 ⚠️ caveat.
```

- The second link is the canonical URL written out; add a labelled second source after it when it helps (`· code <https://...>`).
- Types: *paper*, *benchmark*, *leaderboard*, *repo*, *model*, *API*, *dataset*, *blog*, *docs*, *talk*, *podcast*, *essay*.
- The date is the first public release: arXiv v1, launch post, first release tag, or the date on the page. Write only what the source shows (a month is fine when the day is not given).
- 🆕 marks items released, or given a major new version, in 2025–2026. Caveats go last, after 🆕, as `⚠️ short reason.`
- One line per entry, no hard wraps. Copy the shape of the nearest entry in the section you are adding to.

## How

Open a PR editing `README.md`. One change per PR where possible. In the PR body, say why the item clears the bar, quote the sentence behind any number, and say who judged success and how many trials ran. The daily scan described in [SCAN.md](SCAN.md) also opens PRs; a maintainer reviews every PR before it is merged.
