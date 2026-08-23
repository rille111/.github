# Repo naming + local folder layout

Canonical for all of Rickard's GitHub accounts and for the local layout on titan.
Mirrored in the `github-repo-naming` agent skill — if you change one, change both.
Rewritten 2026-08-23 (B:\Git reorg: buckets, forks folder, GitWorktrees).

## 1. GitHub repo names

```
<Public|Private>.<Category>.<Name>
```

- Visibility token first, matching actual repo visibility. If visibility changes, rename.
- Exactly 3 dot segments; extra dots collapse to hyphens
  (`OpenSource.MassTransit.Extensions` -> `Public.OpenSource.MassTransit-Extensions`).
- No account-name prefix — the account already conveys it.
- Categories: **Websites, Tools, OpenSource, Workshops, Samples, Docs, Customers,
  Desktop, Agents, Backups, Apps** (Apps added 2026-08-23 for the nwo app suite).
- Forks: `Fork.<upstream-repo-name>` (no visibility token). Never rename a fork away
  from upstream's name.
- **Never rename:** `.github` (GitHub-magic org repo), `rille111` (profile README repo,
  must equal username).
- **Trap:** GitHub silently strips a trailing `.git` from repo names
  (`Private.Backups.Git` became `Private.Backups`). Use `.GitRepos` etc.
  Always verify the resulting name after a rename.

Accounts in scope: `rille111`, `kumobits`, `new-appworld-order`.
(`3iluminati` was deleted 2026-08-23.)

## 2. Local layout on titan

```
B:\Git\
  rille111\      clones of rille111 repos          (folder name = exact repo name)
  kumobits\      clones of kumobits repos          (folder name = exact repo name)
  nwo\           clones of new-appworld-order repos (folder name = exact repo name)
  forks\         OUR FORKS, upstream folder names   (hermes-agent, mnemosyne)
  not-mine\      other people's repos, no fork intended (upstream names kept)
  not-decided\   projects not (yet) pushed to any remote
B:\GitWorktrees\ ALL git worktrees, any repo, flat: one folder per worktree, short names
B:\GitWorktrees\runtime\   RESERVED for update-hermes.ps1 generated runtime trees
```

Decision rule for where a thing lives:
- I own the repo on GitHub -> account folder (`rille111\`, `kumobits\`, `nwo\`).
- It is my fork of someone's repo (`Fork.*` on GitHub) -> `forks\`, upstream folder name.
- Someone else's repo, not forking -> `not-mine\`.
- Not pushed anywhere / undecided -> `not-decided\`.
- It is a worktree -> `B:\GitWorktrees\<short-name>`, NEVER anywhere else
  (not B:\TEMP, not Documents, not the repo's parent).
- Nothing else lives loose at `B:\Git` root. No exceptions anymore — the old
  root-level `hermes-agent` / `mnemosyne` exception was retired 2026-08-23; both
  now live in `forks\` and every hardcoded consumer was repointed.

When cloning, clone INTO the right folder directly. When creating a repo, apply
the naming convention without asking; state the chosen name.

## 3. Worktree rules

- Move a worktree ONLY with `git worktree move <src> <dst>` — never Rename-Item /
  mv, which breaks gitdir links in both directions.
- Cross-volume moves: `git worktree move` fails with "Improper link"; instead
  robocopy the tree, `git worktree repair <new-path>` from the main repo, verify
  `git status` matches pre-move, then delete the old copy.
- If the MAIN repo moves: move worktrees first (or after), then from the new main run
  `git worktree repair <wt-path> [<wt-path>...]` listing every worktree — repairs both sides.
- Verify after any move: `git worktree prune --dry-run -v` from the main repo must
  print NOTHING, and every worktree must answer `git -C <wt> rev-parse HEAD`.
- `B:\GitWorktrees\runtime\` is force-cleaned by update-hermes.ps1's
  Invoke-TreeCleanup. NEVER put a dev worktree there — it will be deleted,
  uncommitted work included.

### Hermes runtime specifics (as of 2026-08-23)

- Main fork repo: `B:\Git\forks\hermes-agent` (was `B:\Git\hermes-agent`).
  Hardcoded consumers already repointed: `HERMES_HOME\tools\update-hermes.ps1`
  ($MainRepo/$WtRoot/$WinUnpack), `update-center-status.ps1`, Start Menu `Hermes.lnk`,
  `scripts\hermes-auth-list.ps1`, `scripts\verify-anthropic.ps1`.
- The runtime junction `C:\Users\ADMIN\AppData\Local\hermes\hermes-agent` MOVES —
  always read it live. As of the reorg it still points at the legacy live tree
  `B:\Git\hermes-agent-worktrees\update-v2026.8.19-20260821-184354` (backend was
  running from its venv, so it could not be moved). The next update-hermes.ps1 run
  creates its tree in `B:\GitWorktrees\runtime\` and repoints the junction; after a
  verified update + rollback window, the leftover `B:\Git\hermes-agent-worktrees\`
  folder can be retired manually.
- Desktop exe: `B:\Git\forks\hermes-agent\apps\desktop\release\win-unpacked\Hermes.exe`.

## 4. Phantom directory locks (expected, not corruption)

Titan runs ~10 local AI agents. A folder that another agent holds as its working
directory cannot be renamed/moved: Windows says Access denied while no process
lists the path, ACLs are fine, and writes INSIDE the folder succeed. Do NOT
hunt-and-kill processes to clear these (that pattern zeroed the memory store on
2026-07-31). Retry later — locks clear when the owning agent moves on.

## 5. Audit

`audit-git-layout.ps1` in `kumobits/Private.Agents.Tools` (scripts\) checks all of
this mechanically: GitHub name conformance, local placement vs remote URL, nothing
loose at B:\Git root, worktree health. Run it after any repo/folder operation.

Quick manual checks:

```powershell
# non-conforming GitHub names
foreach ($o in @('rille111','kumobits','new-appworld-order')) {
  gh repo list $o --limit 100 --json name --jq '.[].name' |
    Where-Object { $_ -notmatch '^(Public|Private|Fork)\.' -and $_ -notin @('.github','rille111') } |
    ForEach-Object { "$o/$_" }
}
# anything loose at B:\Git root (should list ONLY the bucket folders)
Get-ChildItem B:\Git -Force | Where-Object { $_.Name -notin @('rille111','kumobits','nwo','forks','not-mine','not-decided','hermes-agent-worktrees') }
# worktree health
git -C B:\Git\forks\hermes-agent worktree prune --dry-run -v   # must be empty
git -C B:\Git\forks\mnemosyne    worktree prune --dry-run -v   # must be empty
```
