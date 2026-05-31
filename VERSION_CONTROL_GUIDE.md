# Version Control Guide

This guide governs every change made in any TeachDiffusion repository, regardless of which sub-system (core, data, training, space) is being modified. If a step here conflicts with what feels faster, follow this guide — speed is not the goal, traceability and reviewability are.

---

## 1. Branch structure

```
main  ←  dev  ←  feat/<name>  |  fix/<name>  |  chore/<name>  |  refactor/<name>  |  docs/<name>
```

- **`main`** — release-only. Protected by ruleset: PR required, linear history, signed commits, no force-push, no deletion. Only updated by merging `dev`.
- **`dev`** — integration branch. Protected by ruleset: PR required, no force-push, no deletion. All short-lived branches PR into `dev`.
- **`feat/* · fix/* · chore/* · refactor/* · docs/*`** — short-lived, one per issue. Deleted after merge.

## 2. Branch naming

| Prefix | When to use | Example |
|---|---|---|
| `feat/` | New feature or capability | `feat/pedagogy-gesture-schema` |
| `fix/` | Bug fix | `fix/decomposer-empty-graph` |
| `chore/` | Tooling, deps, config, CI | `chore/bump-diffusers-0.27` |
| `refactor/` | Restructure with no behaviour change | `refactor/student-model-split` |
| `docs/` | Documentation only | `docs/contributing-guide` |

Names are short, kebab-case, and describe **what the branch does** — not the issue number, not the author.

## 3. Commit message format

[Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <short imperative summary>

<longer explanation — what changed and WHY, not just what. Focus on the
reason, not the diff>.

Closes #N
```

**Types:** `feat`, `fix`, `chore`, `refactor`, `docs`, `test`, `style`
**Scope:** the layer or module touched (`pedagogy`, `reasoning`, `video`, `data-pipeline`, `training`, `space`, `deps`, etc.)

### Examples (from this project's domain)

```
feat(pedagogy): add gesture annotation to teaching steps

Encodes gesture type, pacing, and visual cue directly on each
TeachingStep so the video-diffusion prompt builder can condition on
pedagogical intent rather than only on the text content.

Closes #42
```

```
fix(reasoning): handle empty concept graph in decomposer

The decomposer crashed with a KeyError when a topic had no prerequisites
in the knowledge base. Return an empty plan and log a warning instead so
the orchestrator can fall back to the LLM analogy builder.

Closes #57
```

```
chore(deps): bump diffusers to 0.27.0

Required for Wan 2.2 LoRA loading on PEFT 0.10+. Verified
train_lora.py runs end-to-end on the 480p config.

Closes #61
```

## 4. The mandatory workflow

No exceptions, no shortcuts.

1. **Create a GitHub issue first**
   ```bash
   gh issue create --title "Short title" --body "What needs to happen and why"
   ```

2. **Create a branch from `dev`**
   ```bash
   git checkout dev && git pull origin dev
   git checkout -b feat/<name>
   ```

3. **Write the code.** One logical change per branch. If it grows beyond one concern, split it (see §7 multi-phase features).

4. **Stage only the files you changed**
   ```bash
   git add path/to/file1.py path/to/file2.py
   ```
   Never `git add -A` or `git add .` — that quietly pulls in `.DS_Store`, generated files, debug artefacts, and accidental edits.

5. **Commit with a conventional message that includes `Closes #N`**

6. **Push the branch**
   ```bash
   git push origin feat/<name>
   ```

7. **Wait for CI.** If it fails, fix the failure on the same branch before doing anything else. Never push a broken commit and "fix it in the next PR."

8. **Open a PR into `dev`**
   ```bash
   gh pr create --base dev --head feat/<name> --title "..." --body "..."
   ```

9. **Share the PR URL** and wait for review.

10. **Only merge after the maintainer says "approved" or "merge it".** Contributors don't merge their own PRs.

## 5. PR body template

Always use this exact shape:

```markdown
## Summary
- Bullet 1 — what changed and why
- Bullet 2 — what changed and why
- Bullet 3 — what changed and why  (2–4 bullets max)

## Test plan
- [ ] Manual check N° 1
- [ ] Manual check N° 2
- [ ] Automated tests covering this (file or test name)

Closes #N
```

## 6. CI

- CI must be **green** before a PR is considered ready for review.
- If CI fails, you diagnose and fix it on the same branch. No "this is unrelated, ignore it" — if it's failing on your PR, it's yours to investigate.
- Never skip hooks (`--no-verify`), never bypass signing (`--no-gpg-sign`), never use `[skip ci]` in commit messages.

## 7. Multi-phase features

When a feature is large enough that one PR would be unreviewable (rough rule: >400 lines of substantive change, or touches more than 2 layers), break it into numbered phases.

- One issue per phase, linked from a parent tracking issue.
- One branch per phase: `feat/<name>-phase1`, `feat/<name>-phase2`, …
- Each phase PRs into `dev` independently and gets reviewed independently.
- Phases are merged in order. Don't open phase 2 until phase 1 has merged unless they're truly independent.

Never combine unrelated concerns in one PR — even if they're small. Reviewability is the constraint, not effort.

## 8. Hotfixes vs feature work

Almost everything is feature/fix/chore/refactor/docs work — branch off `dev`, PR into `dev`, ship later via `dev` → `main`.

A **hotfix** is a production-critical bug that can't wait for the normal `dev` → `main` cadence (e.g., breaking bug in a released weight, security issue, broken Space):

1. Branch off `main`: `git checkout main && git pull && git checkout -b fix/<name>-hotfix`
2. Open the issue with label `hotfix`.
3. PR straight into `main`.
4. After merge, immediately back-port to `dev`: open a second PR from `main` → `dev` so the two branches don't diverge.

Hotfixes are rare. If in doubt, treat it as a normal `fix/` branch into `dev`.

## 9. After merge

```bash
git checkout dev && git pull origin dev
git log --oneline | head -5      # confirm the merge landed
git branch -d feat/<name>        # delete local branch
git push origin --delete feat/<name>   # delete remote branch
```

## 10. What "approved" means

- The maintainer (Calyx) merges. Contributors do not click the merge button on their own PRs even if GitHub allows it.
- "LGTM" from another contributor is feedback, not approval to merge.
- The only signal that a PR is ready to merge is the maintainer writing "approved" or "merge it" (or doing the merge themselves).

## 11. Hard prohibitions

- **No force-push** unless the maintainer explicitly asks for it (e.g., rebasing during review).
- **No direct commits to `main` or `dev`** — always go through a branch + PR.
- **No `git add -A` / `git add .`** — stage named files only.
- **No skipping CI, signing, or hooks.**
- **No Claude (or any AI assistant) as `Co-Authored-By:`** in commit trailers.
- **No secrets in commits.** API keys, tokens, credentials — never. If one lands, rotate it immediately and rewrite history.
- **No destroying uncommitted work.** If `git status` shows changes you didn't make, ask the maintainer before touching them.
