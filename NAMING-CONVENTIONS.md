# GitHub Repository Naming Convention

Decided 2026-08-19/20. Applies to both accounts: `rille111` (personal) and `kumobits` (org).

## Pattern

```
<Public|Private>.<Category>.<Name>
```

- **Visibility first** - `Public` or `Private`, matching the repo's actual visibility. Not redundant with the GitHub visibility badge: it makes private/internal repos visually cluster together in a flat repo list, which the badge alone doesn't do.
- **Category** - one word describing what kind of thing this is (see list below). Add a sub-category with another dot if genuinely useful (`Samples.Azure` style), but don't go past 3 segments.
- **Name** - PascalCase or `Kebab-Case`, whatever reads best; hyphens inside a segment are fine, dots only separate the three fields above.

No account-name prefix (`Kumobits.`, `Rille111.`) - the account/org you're looking at already tells you that; repeating it in every repo name is noise.

## Categories in use

| Category | Meaning |
|---|---|
| Websites | Live or in-progress web properties |
| Tools | Utilities, internal apps, scripts |
| OpenSource | Public libraries / extensions meant for others to use |
| Workshops | Teaching material, demos |
| Samples | Snippets, experiments, throwaway code |
| Docs | Cheatsheets, howtos, reference material |
| Customers | Client-specific work |
| Desktop | Desktop app-specific projects |
| Agents | AI agent projects |

## Forks - special case

Forks keep the upstream repo's name, prefixed with `Fork.` instead of `Public`/`Private`:

```
Fork.<upstream-repo-name>
```

Reasoning: a fork's visibility just mirrors upstream, so a Public/Private prefix adds nothing - but the upstream name is valuable for recognizing "this is a fork of X" at a glance. Don't rename a fork to Category.Name style; it makes provenance harder to spot.

Examples: `Fork.hermes-agent`, `Fork.mnemosyne`.

## Examples

| Repo purpose | Name |
|---|---|
| Kumobits marketing site | `Public.Websites.Portfolio` |
| Public library | `Public.OpenSource.MassTransit-Extensions` |
| Public utility | `Public.Tools.Ollama` |
| Teaching material | `Public.Workshops.CQS` |
| Public reference docs | `Public.Docs.CheatSheets` |
| Private snippets/experiments | `Private.Samples.Azure` |
| Internal tool, not ready to publish | `Private.Tools.Gaming` |
| Kumobits internal admin site | `Private.Websites.InternalAdmin` |
| Client-specific work | `Private.Customers.AcmeCorp-Integration` |
| Fork of an upstream project | `Fork.hermes-agent` |

## Notes

- Renames are safe: GitHub auto-redirects the old URL, and existing local clones keep working (though `git remote -v` should be updated to the new URL to avoid confusion later).
- When creating a new repo, name it correctly from the start - no default-then-rename step needed.
- This file is mirrored in both accounts' `.github` repo (`rille111/.github` and `kumobits/.github`) and enforced by Claude via a saved skill on titan.
