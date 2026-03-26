# Explore Skill

This package teaches coding agents how to use Explore's inspect-first agent surface safely.

## Install/use

- Install the packaged CLI so `explore` is on your `PATH`.
- Preferred Codex install path: use the built-in skill installer with `https://github.com/johnnybutler7/johnnybutler-com/tree/main/skills/explore`.
- Repo-local fallback: copy this folder to `$CODEX_HOME/skills/explore`.
- Read [SKILL.md](/Users/johnbutler/git_projects/johnnybutler-com/skills/explore/SKILL.md) for the default workflow.
- Use `explore` as the primary terminal entrypoint.
- If you are working from this Rails checkout directly, `bin/explore` remains a local-development wrapper around the same CLI.
- Prefer `--json` when another agent or script will read the result.
- Slug-scoped reads such as `profile inspect --slug ...` and `content list --slug ...` do not need `EXPLORE_API_KEY`, even when `api.v1_key` is configured.
- Owner-only commands still need `EXPLORE_API_KEY` / `--api-key` or a signed-in session.
- The trusted API key is app-level, not account-specific, and is stored at `api.v1_key`.

## Core commands

```bash
explore profile inspect --slug johnny --json
explore content create-draft --account acme-careers --input tmp/draft.json --json
explore drafts list --account acme-careers --json
explore drafts apply --account acme-careers --draft 12 --json
explore drafts apply-preview --account acme-careers --draft 12 --json
explore drafts update --account acme-careers --draft 12 --input tmp/draft-update.json --json
explore drafts archive --account acme-careers --draft 12 --json
explore onboarding status --account acme-careers --json
explore publish preview --account acme-careers --json
```

## Recipes

- Inspect a profile: start with `profile inspect`, then `content list` if the profile looks sparse. Reserve `EXPLORE_API_KEY` for owner-only flows.
- Audit onboarding readiness: call `onboarding status`, then `manifest next-actions`.
- Review stale Notion sync: call `notion sync-status` before suggesting any share or publish action.
- Create a safe draft: use `content create-draft` when the user wants a new future content item without publishing it.
- Review open drafts: use `drafts list`, then `drafts inspect` before proposing follow-up changes.
- Apply a reviewed draft: use `drafts apply` only after reviewing the draft and its apply preview.
- Preview draft impact: use `drafts apply-preview` before talking about what a draft would change live.
- Refine an open draft: use `drafts update` when the draft should stay private but needs better title, summary, or attributes.
- Archive stale drafts: use `drafts archive` when a draft should stop appearing in open review flows.
- Propose profile improvements: inspect first, then use `content propose-update` with a JSON proposal file.
- Preview publish impact: use `publish preview` to explain blockers or empty states without publishing.
