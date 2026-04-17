# Explore Skill

This package teaches Codex how to use Explore's live workflow safely.

## Install/use

1. Install the Explore CLI so `explore` is on your `PATH`: `gem install exploremyprofile`
2. Install this public Codex skill repo with the built-in skill installer: `https://github.com/johnnybutler7/explore-skill`
3. Run `explore setup`
4. Complete browser signup, sign-in, or approval only when Explore asks for it
5. Return to Codex and use Explore naturally

- Practical note: Explore CLI currently requires Ruby >= 3.2.0. If your machine is older, switch with `rbenv` or `mise` first, then retry the gem install.
- Repo-local fallback: copy this folder to `$CODEX_HOME/skills/explore`.
- Read [SKILL.md](./SKILL.md) for the default workflow.
- Use `explore` as the primary terminal entrypoint. The package name is `exploremyprofile`; the installed command name is `explore`.
- On a fresh install, `explore` defaults to the hosted Explore service at `https://exploremyprofile.com`; only set `EXPLORE_BASE_URL` when you need a different host.
- If you are working from this Rails checkout directly, `bin/explore` remains a local-development wrapper around the same CLI.
- Prefer `--json` when another agent or script will read the result.
- When Codex needs to inspect the real CLI surface, start with `explore commands --json` or `explore <command> --help --agent`.
- Slug-scoped reads such as `profile inspect --slug ...` and `content list --slug ...` do not need `EXPLORE_API_KEY`, even when `api.v1_key` is configured.
- Owner-only commands can reuse credentials saved with `explore setup` or `explore login --account ...`, or still accept `EXPLORE_API_KEY` / `--api-key` as explicit overrides.
- For Codex use, `explore setup` is the normal first-run path. Explore opens the browser only when signup, sign-in, or approval is needed, then later commands reuse the saved credential.
- Re-running `explore login --account ...` for another account still adds or refreshes that explicit account login locally and makes it the default for later owner-only commands that omit `--account`.
- If you do not want Explore to launch the browser for you, use `explore setup --no-browser` or `explore login --no-browser --account ...` and open the printed approval URL yourself.
- `--token-stdin` and `--api-key` remain the manual/scripted fallback when you intentionally want token-based setup.

## Common ways to update your profile

### Ask Codex to update it for you

Use simple asks when you want Codex to handle the update flow for you.

- Import my CV into my Explore profile.
- Add my blog or writing to my Explore profile.
- Inspect my current profile and suggest improvements.

### Edit the profile file directly

Codex can open your Explore profile YAML locally, you can edit it directly in your editor, and then Codex can validate, preview, and apply it back to Explore. This is a great fit for bulk edits, precise control, or refining imported content.

- Open my Explore profile YAML so I can edit it.
- I made some edits, please preview and update my profile on Explore.
- Validate my profile YAML, preview the live diff, and apply it to Explore.

## Sample asks

- Show me my public profile.
- Update my summary and links.

## Product behavior

- Keep the default user experience intent-led. Users should describe the profile outcome they want, not internal Explore mechanics.
- Treat direct local profile YAML editing as a normal, encouraged workflow when the user wants bulk edits, precise control, or to refine imported material they can see directly.
- Reserve `export`, `validate`, `preview`, and `apply` wording for internal explanation, explicit file-edit workflows, scripted workflows, or advanced/manual guidance.
- For profile document edits, use the safer shared-owner workflow: export current profile document, edit locally, validate locally, preview the live diff, then apply explicitly with the expected fingerprint.
- `preview profile` is the normal non-mutating review step before any live apply. If preview says `No live changes detected.`, stop there instead of applying unchanged content.
- When preview returns a recommended apply command, prefer using it directly rather than reconstructing the apply command manually.
- After a successful profile-changing action, proactively return the public profile URL.
- Include the preview URL too when it is available, useful, and can be derived confidently.
- Keep read-only inspect/list responses concise; do not add profile links unless they are especially helpful.

## Advanced/manual commands

```bash
explore setup
explore login --account acme-careers
explore commands --json
explore preview profile --help --agent
explore whoami --json
explore profile inspect --slug johnny --json
explore export profile --account acme-careers --output tmp/profile.yml --json
explore validate tmp/profile.yml --json
explore preview profile tmp/profile.yml --account acme-careers --json
explore apply tmp/profile.yml --account acme-careers --expected-fingerprint <fingerprint> --json
explore onboarding status --account acme-careers --json
explore manifest next-actions --account acme-careers --json
explore notion sync-status --account acme-careers --json
explore publish preview --account acme-careers --json
```

## Recipes

- Inspect a profile: start with `explore profile inspect --slug ... --json`. Use `explore setup` as the default first-run owner-auth path when you want the CLI to remember the credential.
- Audit onboarding readiness: call `onboarding status`, then `manifest next-actions`.
- Review stale Notion sync: call `notion sync-status` before suggesting any share or publish action.
- Update CV, blog, writing, projects, links, or summary content: default to intent-led asks like "Import my CV into my Explore profile" or "Update my summary and links." For bulk edits, precise control, or refining imported content, use the local profile YAML workflow and have Codex validate, preview, and then apply it back to Explore with the expected fingerprint.
- Use `explore login --account ...` when you explicitly want the existing-account login command instead of the default setup path.
- Use `explore setup --no-browser` or `explore login --no-browser --account ...` only when you intentionally want to keep the browser launch manual.
- Preview publish impact: use `publish preview` to explain blockers or empty states without publishing.
