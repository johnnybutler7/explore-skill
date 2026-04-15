# Explore

Use this skill when you need to use Explore from Codex through the official agent surface.

## Goals

- default to a natural-language-first Codex workflow
- identify whether the task is read-only or owner-only
- inspect before proposing changes
- translate simple asks into the right underlying CLI workflow
- prefer proposal/draft-only actions over live mutation
- never publish unless the user explicitly asks and a future publish surface exists

## Default workflow

1. Start with the simple Codex story.
   The normal user path is:
   install the Explore CLI, install this skill, run `explore setup`, complete browser auth only when needed, then continue in natural language.
   Prefer the same sample asks the app recommends:
   - `Set up Explore`
   - `Inspect my Explore profile`
   - `Import my CV into Explore`
   - `Pull in my blog or website and shape the profile`
   - `Show me my public profile`
   - `Update my summary and links`
2. Translate the ask into the right Explore workflow.
   Read-only by slug: inspect public profile/content first.
   Owner-only: reuse the signed-in session created by `explore setup` or explicit login.
   For first-run or reconnecting work in Codex, have the user run `explore setup` in their own terminal so the browser handoff happens there, not in chat.
3. Keep inspect-first safety where it matters.
   Start with current state before proposing changes.
   Check onboarding/sync/readiness state when the request touches setup, launch, or imported content.
4. Use the profile document as the normal structured update path.
   For CV import, blog/website shaping, summary updates, and link updates, the normal flow is:
   export -> edit/map structured content -> validate -> apply.
5. Prefer proposal and draft flows over live mutation when the task calls for a staged change.
6. Only drop to low-level flags or manual auth/token flows when the user explicitly needs advanced or scripted control.

## Natural-language mapping

- `Set up Explore`
  Run or resume `explore setup`; browser auth only when needed.
- `Inspect my Explore profile`
  Inspect public profile/content first, then owner-only state only if the task requires it.
- `Import my CV into Explore`
  Use the profile document flow and preserve structure/tone.
- `Pull in my blog or website and shape the profile`
  Use the profile document flow and map structured content into the right sections.
- `Show me my public profile`
  Inspect or preview the public result, depending on whether the task is public-read or owner-preview.
- `Update my summary and links`
  Inspect current state first, then use the profile document flow or a proposal path.

## Profile document flow

Treat this as the normal V1 update path for profile work:

1. export the profile document
2. edit or map structured content into it
3. validate the document
4. apply the validated result back to Explore

When the request is “import my CV,” “pull in my blog,” “update my summary,” or “update my links,” this is usually the right underlying workflow.

If the task is better handled as a staged draft or proposal, use the draft/proposal surfaces instead of mutating live state directly.

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

## Advanced CLI reference

Use this section when you intentionally need low-level operator control, explicit account selection, script-friendly JSON output, or manual fallback behavior.

- If Explore has `api.v1_key` configured, slug-scoped reads such as `profile inspect --slug ...` and `content list --slug ...` still work without `EXPLORE_API_KEY`.
- Owner-only commands can reuse credentials saved with `explore setup` or `explore login --account ...`, and still accept `EXPLORE_API_KEY` / `--api-key` or a signed-in session.
- If you already know you need the explicit existing-account login path, use `explore login --account ...`.
- If you do not want Explore to launch a browser automatically, use `explore setup --no-browser` or `explore login --no-browser --account ...` and open the printed URL yourself.
- The trusted API key is app-level, not account-specific, and is stored at `api.v1_key`.
- If you are working from the app repo directly, `bin/explore` is still a local wrapper around the same CLI.

### Command patterns

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
