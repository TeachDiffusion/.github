# Contributing to TeachDiffusion

Thanks for being here. TeachDiffusion is built on the belief that the right to understand is a fundamental right — every contribution helps make quality math education more accessible.

This guide is the **friendly version** of how we work. For the strict, step-by-step workflow, see [VERSION_CONTROL_GUIDE.md](VERSION_CONTROL_GUIDE.md).

---

## Setup

Each repository has its own setup. The most common one (core package):

```bash
git clone https://github.com/TeachDiffusion/TeachDiffusion.git
cd TeachDiffusion
pip install -e .[dev]
pytest tests/
```

> **Need an API key?** Reasoning and pedagogy layers call the Anthropic API. Get one at [console.anthropic.com](https://console.anthropic.com/) and export `ANTHROPIC_API_KEY` before running.

For dataset, training, and Space setup, see the README of the respective sibling repo.

---

## The one rule that matters most: **issue first**

Every change — every single one — starts with a GitHub issue. No PR is reviewed if it doesn't reference an issue.

Why: an issue is the place where we agree on the *problem* before anyone spends time on the *solution*. It's how we avoid duplicate work, how new contributors find a starting point, and how the maintainer keeps the roadmap honest.

```bash
gh issue create --title "Decomposer crashes on empty concept graph" \
                --body  "Steps to reproduce, expected vs actual, why it matters."
```

---

## Branch naming, in plain language

Pick the prefix that describes what your branch does:

| You're… | Prefix | Example |
|---|---|---|
| Adding something new | `feat/` | `feat/pedagogy-gesture-schema` |
| Fixing a bug | `fix/` | `fix/decomposer-empty-graph` |
| Updating tooling / deps | `chore/` | `chore/bump-diffusers-0.27` |
| Restructuring without changing behaviour | `refactor/` | `refactor/student-model-split` |
| Writing or updating docs only | `docs/` | `docs/contributing-guide` |

Branches are short-lived. Once your PR merges, the branch goes away.

---

## Commit messages

Use [Conventional Commits](https://www.conventionalcommits.org/). The summary is one line, imperative ("add X", not "added X"). The body explains *why*, not *what* — the diff already shows what.

End every commit with `Closes #N`.

### Three examples from this project

```
feat(pedagogy): add gesture annotation to teaching steps

Encodes gesture, pacing, and visual cue on each TeachingStep so the
video-diffusion prompt builder can condition on pedagogical intent
rather than only the text content.

Closes #42
```

```
fix(reasoning): handle empty concept graph in decomposer

KeyError when a topic had no prerequisites in the knowledge base.
Return an empty plan and warn so the orchestrator can fall back to
the LLM analogy builder.

Closes #57
```

```
docs(data): document dataset card and per-clip provenance

Surface docs/dataset_card.md and docs/annotation_guide.md from the
README, and explain how CC-BY attribution flows through the manifest.

Closes #34
```

---

## Pull request checklist

Before you ask for a review, your PR should:

- [ ] Reference an issue with `Closes #N` in the body
- [ ] Target `dev` (not `main`)
- [ ] Have a green CI run
- [ ] Use the PR body template (Summary + Test plan, see [VERSION_CONTROL_GUIDE.md](VERSION_CONTROL_GUIDE.md#5-pr-body-template))
- [ ] Touch one logical concern only — if it grew, split it
- [ ] Include tests for new logic where reasonable
- [ ] Update docs (README, docs/, comments) if the change is user-visible
- [ ] Stage only the files you changed (no `.DS_Store`, no generated artefacts)

What reviewers look for, in order: correctness → tests → readability → docs → style. Don't worry about style first — readability matters more.

---

## What NOT to do

- ❌ **Don't force-push** unless the maintainer specifically asks.
- ❌ **Don't push directly to `main` or `dev`** — both are protected, both require a PR.
- ❌ **Don't `git add -A` or `git add .`** — stage files by name so you know what you're shipping.
- ❌ **Don't merge your own PR** even if GitHub lets you. The maintainer says "approved" or "merge it", then merges.
- ❌ **Don't skip CI, hooks, or signing.** If CI is failing, fix it.
- ❌ **Don't bundle unrelated changes** into one PR ("while I was here…"). Open a separate issue and PR for each concern.
- ❌ **Don't commit secrets.** API keys, tokens, credentials — ever. If you do by accident, tell the maintainer immediately so the key can be rotated.
- ❌ **Don't credit AI assistants as commit co-authors.** If you used Claude, ChatGPT, or any other assistant, the work and the responsibility for it are still yours.

---

## How to ask questions

- **Discussion about a feature or design** — open a GitHub issue with the `discussion` label.
- **Quick clarification on an existing issue or PR** — comment on it directly.
- **Bug you're not sure is a bug** — open an issue anyway, label it `question`, describe what you saw and what you expected.

There are no dumb questions. Better to ask early than waste a weekend going the wrong direction.

---

## Code of conduct

Be patient, be precise, be kind. Help other contributors as you'd want to be helped. Disagreements about technical decisions are normal — disagreements about people are not. See [CODE_OF_CONDUCT.md](https://github.com/TeachDiffusion/TeachDiffusion/blob/main/CODE_OF_CONDUCT.md) in the core repo for the long version.

---

## Thank you

Every issue, every PR, every doc fix moves this project closer to the goal: free, high-quality math education for anyone who wants it. That's worth doing carefully.
