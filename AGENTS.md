# Project notes for agents

Deliberate decisions in this repo - do NOT silently revert them:

- `homebrew.onActivation.cleanup = "zap"` in `configuration.nix` is intentional. It forces the good habit of declaring every Homebrew package in the Nix config instead of installing things ad-hoc, which keeps the machine reproducible. Do not soften it to `uninstall` or `none`. Users are warned about its effect in README.md; this note is for anyone tempted to change the setting itself.
- Never commit `.no-mistakes/` validation evidence to this public repo. `.no-mistakes/` is gitignored; if a validation pipeline stages evidence into a branch, drop it before merging.

## Rebuild failure modes

- Activation runs with `set -e` and does Homebrew *before* home-manager, so any brew failure
  aborts the rebuild before dotfiles are linked. The visible symptom is missing shell config
  (no aliases, default prompt, stale `$EDITOR`), not a Homebrew error. Check whether
  `~/.zshrc` exists before debugging shell problems - if it is absent, activation never
  finished and the real failure is earlier in the log.
- `nix-homebrew` pins the brew binary through `brew-src` in `flake.lock`, but taps are
  unpinned (`mutableTaps = true`), so cask definitions come from the live API and can outrun
  it. `undefined method '<x>' for Cask` is that skew; fix with `nix flake update nix-homebrew`,
  not by dropping the cask - under `cleanup = "zap"` an undeclared cask gets zapped along with
  its application data.

## Maintaining this file

Keep this file for knowledge useful to almost every future agent session in this project.
Do not repeat what the codebase already shows; point to the authoritative file or command instead.
Prefer rewriting or pruning existing entries over appending new ones.
When updating this file, preserve this bar for all agents and keep entries concise.
