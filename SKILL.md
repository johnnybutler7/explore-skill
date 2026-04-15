# Explore

Use this skill when you need to inspect or operate Explore account/profile state through the official agent surface.

## Goals

- identify whether the task is read-only or owner-only
- inspect before proposing changes
- prefer proposal/draft-only actions over live mutation
- never publish unless the user explicitly asks and a future publish surface exists

## Default workflow

1. Decide the context.
   Read-only by slug: use `profile inspect` or `content list` with `--slug`.
   Owner-only: use `--account` with the signed-in session or trusted API key.
   If Explore has `api.v1_key` configured, slug-scoped reads such as `profile inspect --slug ...` and `content list --slug ...` still work without `EXPLORE_API_KEY`.
   Owner-only commands can reuse credentials saved with `explore setup` or `explore login --account ...`, and still accept `EXPLORE_API_KEY` / `--api-key` or a signed-in session.
   For first-run or reconnecting work in Codex, run `explore setup` in your own terminal first so the browser handoff happens there, not in chat.
   If you already know you want the explicit existing-account login path, use `explore login --account ...`.
   If you do not want Explore to launch a browser automatically, use `explore setup --no-browser` or `explore login --no-browser --account ...` and open the printed URL yourself.
   The trusted API key is app-level, not account-specific, and is stored at `api.v1_key`.
2. Read first.
   Start with `explore profile inspect ... --json`.
3. Check workflow state when the task touches setup or readiness.
   Use `explore onboarding status ... --json`.
4. Check sync health before suggesting launch or copy changes sourced from Notion.
   Use `explore notion sync-status ... --json`.
5. When safe next actions matter, use `explore manifest next-actions ... --json`.
6. If suggesting a change, preserve the user's tone and structure.
7. Prefer `explore content propose-update ... --input <file.json> --json` over any live mutation.
8. If the user wants a new content item prepared inside Explore, prefer `explore content create-draft ... --input <file.json> --json`.
9. If the task touches an existing draft, inspect it first with `explore drafts list` and `explore drafts inspect`.
10. If the task asks what a draft would change, use `explore drafts apply-preview ... --json` before describing impact.
11. If the user explicitly wants the reviewed draft turned into real Explore data, use `explore drafts apply ... --json`.
12. If an open draft needs refinement, use `explore drafts update ... --input <file.json> --json`.
13. If a draft is stale or superseded, use `explore drafts archive` instead of deleting or publishing it.
14. If you are working from this app repo directly, `bin/explore` is still a local wrapper around the same CLI.

## Safety rules

- Never treat slug-scoped profile/content reads as permission to access private onboarding or sync state.
- Never assume owner-only commands can use public slug access; onboarding, sync, manifest, publish preview, draft commands, and change proposals still need owner auth.
- Never assume Explore has a full draft publishing model yet.
- `publish preview` explains impact and blockers; it does not publish.
- If onboarding is blocked, explain:
  - what is blocked
  - why it is blocked
  - the next safe action
- If sync is stale or failed, say that clearly before suggesting review or sharing.
- Use manifest/next-actions when present instead of inventing a workflow recommendation.

## Command patterns

```bash
explore setup
explore login --account acme-careers
explore whoami --json
explore profile inspect --slug johnny --json
explore content list --slug johnny --json
explore content create-draft --account acme-careers --input tmp/draft.json --json
explore drafts list --account acme-careers --json
explore drafts inspect --account acme-careers --draft 12 --json
explore drafts apply --account acme-careers --draft 12 --json
explore drafts apply-preview --account acme-careers --draft 12 --json
explore drafts update --account acme-careers --draft 12 --input tmp/draft-update.json --json
explore drafts archive --account acme-careers --draft 12 --json
explore onboarding status --account acme-careers --json
explore manifest next-actions --account acme-careers --json
explore notion sync-status --account acme-careers --json
explore publish preview --account acme-careers --json
explore content propose-update --account acme-careers --input tmp/proposal.json --json
```

## Proposal payload shape

```json
{
  "proposal": {
    "target_type": "profile",
    "summary": "Tighten the headline and summary while preserving the current tone.",
    "attributes": {
      "public_tagline": "Design systems and shipping support for teams in transition",
      "public_bio": "A concise rewrite that keeps the existing voice but makes the offer clearer."
    }
  }
}
```

## Draft payload shape

```json
{
  "draft": {
    "target_type": "project",
    "title": "Internal developer portal rollout",
    "summary": "A new project draft that should stay private until reviewed.",
    "attributes": {
      "title": "Internal developer portal rollout",
      "summary": "A new project draft that should stay private until reviewed."
    }
  }
}
```

## Draft update payload shape

```json
{
  "draft": {
    "title": "Refined project draft",
    "summary": "Keep the draft private but sharpen the framing.",
    "attributes": {
      "summary": "Keep the draft private but sharpen the framing."
    }
  }
}
```
