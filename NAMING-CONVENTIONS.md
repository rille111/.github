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
---

# Local folder layout on titan (`B:\Git`)

The repo-name convention above governs GitHub. This section governs the local disk, because
without it repos for the same account end up in two places at once.

## Rule

```
B:\Git\<github-account>\<exact-repo-name>
```

- One folder per GitHub account/org: `rille111\`, `kumobits\`, `new-appworld-order\`.
- Inside it, the folder name is **exactly** the GitHub repo name (`Private.Websites.PrismoChat`).
  The account folder is what makes the account prefix redundant in repo names.
- `_external\` — clones of other people's repos (upstream names kept, never renamed).
- Underscore prefix sorts non-project folders to the top.

## Exception: the Hermes fork

`B:\Git\hermes-agent` (GitHub: `Fork.hermes-agent`) and `B:\Git\mnemosyne` (GitHub:
`Fork.mnemosyne`) keep their upstream folder names and stay at the root. Reasons:

- `hermes-agent` is the shared main repo for ~28 git worktrees whose `.git` files hold absolute
  paths to it, the running desktop exe lives under it
  (`apps\desktop\release\win-unpacked\Hermes.exe`), and three hardcoded paths in
  `HERMES_HOME\tools\update-hermes.ps1` and `update-center-status.ps1` point at it.
- Renaming buys nothing (the `fork` remote already points at `Fork.hermes-agent`) and risks the
  daily driver.

**Worktrees belong in `B:\Git\hermes-agent-worktrees\<short-name>`, never loose at the root.**
Move them with `git worktree move <src> <dst>` — never `Rename-Item`/`mv`, which breaks the
gitdir links in both directions. Verify with `git worktree prune --dry-run -v` (empty = healthy).

## Known trap: phantom directory locks

Titan runs ~10 local AI agents sharing this machine. A folder an agent currently has as its
working directory cannot be renamed or moved — Windows reports `Access denied` / `being used by
another process` even though no process lists that path as its executable and nothing references
it in config. This is expected, not corruption. Do NOT hunt-and-kill processes to clear it; retry
later, or when the owning agent is idle.