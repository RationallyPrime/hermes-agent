# Rebase from NousResearch upstream

This fork tracks `NousResearch/hermes-agent`, but Sokrates consumes
`RationallyPrime/hermes-agent` so local trims and dependency fixes can remain
stable across upstream updates.

## One-time remote setup

```bash
git remote add upstream https://github.com/NousResearch/hermes-agent.git
git fetch upstream --tags
```

## Rebase procedure

1. Start from a clean worktree.

   ```bash
   git status --short
   ```

2. Fetch upstream and rebase onto the target upstream ref or tag.

   ```bash
   git fetch upstream --tags
   git rebase upstream/main
   ```

3. Resolve conflicts with the fork policy:

   - Keep fork deletions for upstream-only release helpers, contributor audit
     workflow files, and other scripts intentionally removed in the trim phase.
   - Preserve OpenRouter, Anthropic, OpenAI Codex, custom, and local model
     providers as the first-class provider surface. Do not reintroduce removed
     hosted provider shortcuts unless Sokrates explicitly needs them.
   - Keep browser automation, RL/SFT environments, and sample data unless a new
     inventory pass proves they are separable and unnecessary.
   - Re-run `uv lock` whenever `pyproject.toml` or dependency metadata changes.

4. Run the fast local checks.

   ```bash
   uv sync --extra web
   uv run hermes --help
   uv run hermes --version
   uv run hermes dashboard --help
   ```

5. Push the rebased fork.

   ```bash
   git push --force-with-lease origin main
   ```

6. Update the Sokrates flake lock after the fork moves.

   ```bash
   cd /home/rationallyprime/projects/sokrates
   nix flake update nix-hermes
   nix build .#nixosConfigurations.sokrates-dev.config.system.build.toplevel --no-link --dry-run
   ```

Commit the Sokrates `flake.nix` and `flake.lock` update separately from any
fork-side rebase work.
