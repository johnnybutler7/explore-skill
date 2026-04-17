# Explore Skill

This package teaches Codex how to use Explore's live workflow safely with the same natural-language-first approach the product now recommends.

## Quick start

1. Install the Explore CLI so `explore` is on your `PATH`:
   `gem install exploremyprofile`
2. Install this skill into Codex:
   `Use $skill-installer to install: https://github.com/johnnybutler7/explore-skill`
3. Run `explore setup`
4. Complete browser signup, sign-in, or approval only when Explore asks for it
5. Return to Codex and ask naturally

Ruby note: Explore CLI currently requires Ruby >= 3.2.0.

The RubyGems package is `exploremyprofile`; the installed command name is `explore`.
On a fresh install, `explore` defaults to `https://exploremyprofile.com`; only set `EXPLORE_BASE_URL` when you need a different host.

## Use Explore in Codex

After `explore setup`, keep the default workflow simple. Ask Codex naturally and let the CLI handle the underlying workflow.

### Common ways to update your profile

- `Set up Explore`
- `Inspect my Explore profile`
- `Import my CV into Explore`
- `Pull in my blog or website and shape the profile`
- `Show me my public profile`
- `Update my summary and links`

The normal V1 path is:

1. run `explore setup`
2. let the browser handle auth only when needed
3. ask Codex for the profile outcome you want
4. let Codex translate that into the right Explore CLI flow

Read [SKILL.md](./SKILL.md) for the default Codex behavior.

## How the skill works

- Natural language is the default interface.
- The Explore CLI is the substrate underneath.
- Browser handoff happens only when setup/login needs it.
- Profile updates normally flow through the profile document: export, map/edit structured content, validate, preview, then apply explicitly with the expected fingerprint.
- Raw flags and operator-style commands still exist, but they are secondary to the first-read experience.

## Advanced/manual commands

Use these commands when you intentionally want to drive the CLI yourself, script around it, or troubleshoot a manual path.

- If you are working from the main Explore app repo directly, `bin/explore` remains a local wrapper around the same CLI.
- Prefer `--json` when another agent or script will read the result.
- When Codex needs to inspect the real CLI surface, start with `explore commands --json` or `explore <command> --help --agent`.
- Slug-scoped reads such as `profile inspect --slug ...` and `content list --slug ...` do not need `EXPLORE_API_KEY`, even when `api.v1_key` is configured.
- Owner-only commands can reuse credentials saved with `explore setup` or `explore login --account ...`, or still accept `EXPLORE_API_KEY` / `--api-key` as explicit overrides.
- Re-running `explore login --account ...` for another account refreshes that explicit account login locally and makes it the default for later owner-only commands that omit `--account`.
- If you do not want Explore to launch the browser for you, use `explore setup --no-browser` or `explore login --no-browser --account ...` and open the printed approval URL yourself.
- `--token-stdin` and `--api-key` remain the manual/scripted fallback when you intentionally want token-based setup.

### Core commands

```bash
explore setup
explore login --account acme-careers
explore commands --json
explore preview profile --help --agent
explore whoami --json
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

### Profile document flow

For CV import, blog import, profile reshaping, summary updates, and link updates, the normal flow is:

1. `explore export profile --account <account> --output tmp/profile.yml --json`
2. edit or map structured content into the profile document
3. `explore validate tmp/profile.yml --json`
4. `explore preview profile tmp/profile.yml --account <account> --json`
5. if preview reports changes, prefer the recommended apply command it returns, including `--expected-fingerprint ...`
6. otherwise apply explicitly with the expected fingerprint from preview:
   `explore apply tmp/profile.yml --account <account> --expected-fingerprint <fingerprint> --json`

`preview profile` is the normal non-mutating review step before any live apply. If preview says `No live changes detected.`, stop there instead of applying unchanged content.

### Other manual recipes

- Inspect a profile: start with `profile inspect`, then `content list` if the profile looks sparse.
- Audit onboarding readiness: call `onboarding status`, then `manifest next-actions`.
- Review stale Notion sync: call `notion sync-status` before suggesting any share or publish action.
- Create a safe draft: use `content create-draft` when the user wants a new future content item without publishing it.
- Review open drafts: use `drafts list`, then `drafts inspect` before proposing follow-up changes.
- Apply a reviewed draft: use `drafts apply` only after reviewing the draft and its apply preview.
- Preview draft impact: use `drafts apply-preview` before talking about what a draft would change live.
- Refine an open draft: use `drafts update` when the draft should stay private but needs better title, summary, or attributes.
- Archive stale drafts: use `drafts archive` when a draft should stop appearing in open review flows.
- Propose profile improvements: inspect first, then use `content propose-update` with a JSON proposal file when a staged suggestion makes more sense than the profile document workflow.
- Preview publish impact: use `publish preview` to explain blockers or empty states without publishing.
