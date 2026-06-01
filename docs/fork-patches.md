# Fork Patch Stack

This fork keeps local patches as a small linear stack on `main` above upstream
`mautrix/go:v0.16.x`.

Do not fold the patches into upstream snapshots or make ad hoc edits in the
deployed checkout. Put every local behavior change in its own commit on `main`
so it can be replayed cleanly when upstream moves.

## Upstream

- Upstream repo: `mautrix/go`
- Upstream branch: `v0.16.x`
- Maintained fork branch: `keithah/mautrix-go:main`
- Sync branch used by automation: `sync/upstream-v0.16.x`

## Runtime Patches

These are the behavior patches that must continue to apply:

1. `bridge: pass relay reactions to portals`

The Discord fork depends on this hook until it lands upstream and
mautrix-discord updates its mautrix/go dependency.

## Maintenance Patches

The fork also contains maintenance-only commits, such as this document and the
GitHub Actions workflow. Those commits are intentionally part of the fork stack
so a rebuilt `main` still has the automation.

## Updating Manually

The GitHub Action does this daily. To reproduce locally:

```sh
git switch main
git pull --ff-only origin main
scripts/forward-port-fork.sh
go test ./...
git push origin sync/upstream-v0.16.x
```

If the result looks good, merge the generated PR into `main`, or fast-forward
`main` to the sync branch:

```sh
git switch main
git merge --ff-only sync/upstream-v0.16.x
git push origin main
```

If cherry-picking conflicts, resolve the conflict in the sync branch, continue
with `git cherry-pick --continue`, run tests, then update `main`.
