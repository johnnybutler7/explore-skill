# Explore Skill

This package teaches coding agents how to use Explore's inspect-first agent surface safely.

## Install/use

- Install the published RubyGems package so `explore` is on your `PATH`: `gem install exploremyprofile`.
- Preferred Codex install path: use the built-in skill installer with `https://github.com/johnnybutler7/explore-skill`.
- Repo-local fallback: copy this folder to `$CODEX_HOME/skills/explore`.
- Read [SKILL.md](./SKILL.md) for the default workflow.
- Use `explore` as the primary terminal entrypoint. The package name is `exploremyprofile`; the installed command name is `explore`.
- On a fresh install, `explore` defaults to the hosted Explore service at `https://exploremyprofile.com`; only set `EXPLORE_BASE_URL` when you need a different host.
- If you are working from the main Explore app repo directly, `bin/explore` remains a local-development wrapper around the same CLI.
- Prefer `--json` when another agent or script will read the result.
- Slug-scoped reads such as `profile inspect --slug ...` and `content list --slug ...` do not need `EXPLORE_API_KEY`, even when `api.v1_key` is configured.
- Owner-only commands can reuse credentials saved with `explore setup` or `explore login --account ...`, or still accept `EXPLORE_API_KEY` / `--api-key` as explicit overrides.
- Re-running `explore login --account ...` for another account still adds or refreshes that explicit account login locally and makes it the default for later owner-only commands that omit `--account`.
- For Codex/chat-based use, prefer running `explore setup` in your own terminal first, approve signup, sign-in, or approval in the browser there, then return to Codex and let later commands reuse the stored credentials without pasting secrets into chat.
- If you do not want Explore to launch the browser for you, use `explore setup --no-browser` or `explore login --no-browser --account ...` and open the printed approval URL yourself.
- `--token-stdin` and `--api-key` remain the manual/scripted fallback when you intentionally want token-based setup.

## Core commands

```bash
explore setup
explore login --account acme-careers
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

## Recipes

- Inspect a profile: start with `profile inspect`, then `content list` if the profile looks sparse. Use `explore setup` as the default first-run owner-auth path when you want the CLI to remember the credential. Use `explore login` when you explicitly want the existing-account login command. For Codex, do that setup/login in your own terminal first. For scripts, pipe the token with `--token-stdin`.
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
