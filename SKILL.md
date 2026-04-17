---
name: "explore"
description: "Use when you need to inspect or operate Explore account/profile state through the official agent surface."
---

# Explore

Use this skill when you need to inspect or operate Explore account/profile state through the official agent surface.

## Goals

- identify whether the task is read-only or owner-only
- inspect before proposing changes
- use the live setup path by default
- keep the user-facing ask intent-led and natural-language-first
- use the local profile YAML workflow as a normal, encouraged path for precise or bulk changes
- guide profile edits through the current shared-owner-safe workflow
- never publish unless the user explicitly asks and a future publish surface exists

## Default workflow

1. Decide the context.
   Read-only by slug: use `profile inspect` or `content list` with `--slug`.
   Owner-only: use `--account` with the signed-in session or trusted API key.
   If Explore has `api.v1_key` configured, slug-scoped reads such as `profile inspect --slug ...` and `content list --slug ...` still work without `EXPLORE_API_KEY`.
   Owner-only commands can reuse credentials saved with `explore setup` or `explore login --account ...`, and still accept `EXPLORE_API_KEY` / `--api-key` or a signed-in session.
   For first-run or reconnecting work in Codex, start with `explore setup`.
   Explore opens the browser only when signup, sign-in, or approval is needed, then the workflow returns to the agent terminal.
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
7. For CV imports, blog imports, writing updates, project updates, links, and summary changes, keep the user-facing framing intent-led.
   Prefer asks like:
   - "Import my CV into my Explore profile."
   - "Add my blog or writing to my Explore profile."
   - "Inspect my current profile and suggest improvements."
   - "Show me my public profile."
   - "Update my summary and links."
8. When the user wants precise control, bulk edits, or to refine imported content directly, offer the local profile YAML workflow as a normal, first-class option.
   Prefer wording like:
   - "Open my Explore profile YAML so I can edit it."
   - "I made some edits, please update my profile on Explore."
   - "Open my profile file and help me tighten my summary and experience."
9. Do not over-push CV import when direct local profile editing is the cleaner route.
10. Do not make primary sample prompts lead with `export`, `validate`, `preview`, or `apply` unless the user is already in an explicit file-edit or manual workflow.
11. When you need to implement a profile change through the direct-edit path, use the local profile YAML as the normal editable surface underneath.
12. Export the current document with `explore export profile --account <slug> --output tmp/profile.yml --json`.
13. Let the user edit `tmp/profile.yml` directly when they want to.
14. Validate with `explore validate tmp/profile.yml --json`.
15. Preview with `explore preview profile tmp/profile.yml --account <slug> --json`.
16. If preview returns a recommended apply command, prefer using it directly.
17. Otherwise apply with `explore apply tmp/profile.yml --account <slug> --expected-fingerprint <fingerprint> --json`.
18. If preview says `No live changes detected.`, stop there instead of applying unchanged content.
19. After a successful profile-changing action, proactively return:
   - a short success confirmation
   - the public profile URL when it can be derived confidently
   - the preview URL too when it is available and useful
   - at most one short next step
20. Do not add profile links by default for read-only inspect/list actions unless they are especially helpful.
21. Use draft/proposal commands only when the task specifically needs those narrower surfaces.
22. If you are working from this app repo directly, `bin/explore` is still a local wrapper around the same CLI.

## Safety rules

- Never treat slug-scoped profile/content reads as permission to access private onboarding or sync state.
- Never assume owner-only commands can use public slug access; onboarding, sync, manifest, publish preview, draft commands, and change proposals still need owner auth.
- Never skip inspect-first review before applying a profile document change.
- Never skip `preview profile` before a profile apply unless the user explicitly asks for a lower-level manual path.
- Never apply a profile document without the expected fingerprint from the immediately preceding preview.
- When `preview profile` returns a recommended apply command, prefer it over reconstructing the apply command manually.
- Never assume arbitrary source material maps cleanly; keep the mapped result structured, validate it, and explain any unsupported gaps honestly.
- Never assume Explore has a full draft publishing model yet.
- `publish preview` explains impact and blockers; it does not publish.
- If onboarding is blocked, explain:
  - what is blocked
  - why it is blocked
  - the next safe action
- If sync is stale or failed, say that clearly before suggesting review or sharing.
- Use manifest/next-actions when present instead of inventing a workflow recommendation.
- Never invent public or preview links; only return them when the active account/slug makes them derivable with confidence.

## Command patterns

```bash
explore setup
explore login --account acme-careers
explore commands --json
explore preview profile --help --agent
explore whoami --json
explore profile inspect --slug johnny --json
explore content list --slug johnny --json
explore export profile --account acme-careers --output tmp/profile.yml --json
explore validate tmp/profile.yml --json
explore preview profile tmp/profile.yml --account acme-careers --json
explore apply tmp/profile.yml --account acme-careers --expected-fingerprint <fingerprint> --json
explore onboarding status --account acme-careers --json
explore manifest next-actions --account acme-careers --json
explore notion sync-status --account acme-careers --json
explore publish preview --account acme-careers --json
```

## Profile document workflow

Use this as a normal, encouraged workflow when the user wants bulk edits, precise control, or easy review of the profile document they are changing:

1. `explore export profile --account <slug> --output tmp/profile.yml --json`
2. open `tmp/profile.yml` locally so the user can edit it directly, or help them refine the document in place
3. `explore validate tmp/profile.yml --json`
4. `explore preview profile tmp/profile.yml --account <slug> --json`
5. if preview returns a recommended apply command, prefer using it directly
6. otherwise use `explore apply tmp/profile.yml --account <slug> --expected-fingerprint <fingerprint> --json`

Use that flow for:

- CV import and refresh
- blog or writing import
- projects, links, tagline, and summary updates
- profile cleanup that already fits the supported schema

Keep the user-facing wording intent-led even when you choose this path:

- offer to open the Explore profile YAML locally
- let the user edit it directly when they want hands-on control
- then validate, preview, and apply the changes back to Explore

Keep the browser role light:

- `explore setup` handles the normal first-run handoff
- the browser should only appear when signup, sign-in, or approval is needed
- after that, return to the agent workflow and continue in terminal/Codex

## Secondary owner commands

These still exist, but they are not the primary user-facing path for normal profile updates:

```bash
explore drafts list --account acme-careers --json
explore drafts inspect --account acme-careers --draft 12 --json
explore drafts apply-preview --account acme-careers --draft 12 --json
explore drafts apply --account acme-careers --draft 12 --json
explore drafts update --account acme-careers --draft 12 --input tmp/draft-update.json --json
explore drafts archive --account acme-careers --draft 12 --json
explore content propose-update --account acme-careers --input tmp/proposal.json --json
```
