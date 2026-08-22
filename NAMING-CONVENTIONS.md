# GitHub Repository Naming Convention

Applies to both accounts: `rille111` (personal) and `kumobits` (org). Fully applied 2026-08-22.

## Pattern

```
<Public|Private>.<Category>.<Name>
```

- **Visibility first** - `Public` or `Private`, matching the repo's actual visibility. Not redundant with GitHub's visibility badge: it makes private/internal repos cluster together when sorting a flat repo list, which the badge alone doesn't do.
- **Category** - one word describing what kind of thing this is (see list below).
- **Name** - PascalCase or Kebab-Case, whichever reads best. Hyphens go *inside* a segment; dots only separate the three fields above.

Exactly 3 dot-separated segments. If a name previously had extra dots (`OpenSource.MassTransit.Extensions`), collapse the tail with a hyphen: `Public.OpenSource.MassTransit-Extensions`.

No account-name prefix (`Kumobits.`, `Rille111.`) - the account/org you're browsing already tells you that; repeating it in every repo name is noise.

## Categories in use

| Category | Meaning |
|---|---|
| Websites | Live or in-progress web properties |
| Tools | Utilities, internal apps, scripts |
| OpenSource | Public libraries / extensions meant for others to use |
| Workshops | Teaching material, demos |
| Samples | Snippets, experiments, old project code |
| Docs | Cheatsheets, howtos, reference material |
| Customers | Client-specific work |
| Desktop | Desktop app projects |
| Agents | AI agent projects |
| Backups | Archived data / backup artifacts |

## Forks - special case

Forks keep the upstream repo name, prefixed with `Fork.` instead of `Public`/`Private`:

```
Fork.<upstream-repo-name>
```

Reasoning: a fork visibility just mirrors upstream, so a Public/Private prefix adds nothing - but the upstream name preserves provenance and makes "this is a fork of X" obvious at a glance.

Current forks: `Fork.hermes-agent`, `Fork.mnemosyne`.

## Exemptions - do NOT rename these

| Repo | Why |
|---|---|
| `.github` | GitHub-magic name; powers org-wide defaults and community health files. Renaming breaks it. |
| `rille111` (on account rille111) | Profile README repo - must exactly match the username or the profile README stops rendering. |

## Hard constraint: no trailing .git

GitHub silently strips a trailing `.git` (case-insensitive) from repo names, since that suffix is reserved for clone URLs. `Private.Backups.Git` becomes `Private.Backups` on rename with no warning. Never end a repo name with `.Git` - use `.GitRepos` or similar.

## Examples

| Repo purpose | Name |
|---|---|
| Kumobits marketing site | `Private.Websites.Kumobits` |
| Public library | `Public.OpenSource.MassTransit-Extensions` |
| Public utility | `Public.Tools.Ollama` |
| Teaching material | `Public.Workshops.CQS` |
| Public reference docs | `Public.Docs.CheatSheetsCliCommandsHowTos` |
| Private snippets/experiments | `Private.Samples.Azure` |
| Internal tool, not public-ready | `Private.Tools.Gaming` |
| Client-specific work | `Private.Customers.Patricia` |
| Backup archives | `Private.Backups.GitRepos` |
| Fork of an upstream project | `Fork.hermes-agent` |

## Notes

- Renames are safe: GitHub auto-redirects the old URL and existing local clones keep working. Still update `git remote set-url` locally to avoid confusion later.
- Name new repos correctly at creation - no default-then-rename step.
- If visibility changes, rename the repo to match (`Private.X.Y` -> `Public.X.Y`).
- This file is mirrored in `rille111/.github` and `kumobits/.github`, and enforced by Claude via the `github-repo-naming` skill on titan. Keep all three in sync.