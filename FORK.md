# Vladkarok/omasnap fork

This fork of [omacom/omasnap](https://github.com/omacom/omasnap) carries a few
local patches. The `patched` branch (the default here) is upstream `main` with
these commits on top. `local/save-as` keeps the original branch as it was
before the first rebase.

## Patches

| Commit subject | What it does |
| --- | --- |
| Keep the theme loader from outliving quick exits | Joins the theme worker on destruction so `--version`/`--help` no longer crash on exit |
| Isolate the smoke runner from the user's omasnap.conf | The smoke tests run with `QStandardPaths` test mode, so your real config can't change the results |
| Reveal Save As under the Save button on hover | Puts Save As in the editor next to Save |
| Remember the Save As folder across runs | Saves the last Save As folder in `~/.local/state/Omarchy/omasnap/save-as-dir` |
| Open the Save As menu opposite the Save tooltip | Fixes the menu position |
| Show Save As as a fixed button stacked on Save | Makes Save As a permanent button instead of a hover reveal |
| Add Save As to the capture preview | Save As button and `S` key in the floating preview (pin) |
| Let OMASNAP_NOTIFY=0 keep the timed preview | `OMASNAP_NOTIFY=0` in the environment beats `--notify`, so you get the preview instead of a notification |

The last patch is what `hl.env("OMASNAP_NOTIFY", "0")` in
`~/.config/hypr/envs.lua` relies on.

## Rebasing onto a new upstream release

```sh
git fetch origin --tags
git switch patched
git rebase v1.X.Y            # or origin/main
# Conflict: fix it, `git add`, `git rebase --continue`.
# If upstream now does what a patch does, `git rebase --skip` that commit.
make smoke BUILD_DIR=build/patched
git push --force-with-lease fork patched
```

Remotes: `origin` is omacom/omasnap (upstream) and `fork` is this repository.

## Build and test

```sh
make build BUILD_DIR=build/patched     # CMake + Ninja, out of source
make smoke BUILD_DIR=build/patched     # offscreen smoke tests, silent on success
build/patched/omasnap --version
```

## Install

```sh
./install-omarchy
```

This installs the build dependencies with `omarchy-pkg-add`, builds in
`~/.cache/omasnap/build` and installs to `~/.local` (override with
`OMASNAP_PREFIX`). `~/.local/bin` comes before `/usr/bin` on PATH, for the
shell and for Hyprland too, so this build overrides the pacman `omasnap`
package. To go back to the package, delete `~/.local/bin/omasnap`.
