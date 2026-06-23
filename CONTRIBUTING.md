# Contributing

Thanks for helping improve Exploratory Data Analysis.

## Good Contributions

- Add a focused EDA workflow for a common tabular-data situation.
- Improve report quality, caveat handling, or Mermaid chart guidance.
- Add small, realistic example datasets that do not contain private data.
- Tighten language in `SKILL.md` so Codex behaves more reliably.
- Report cases where the skill overclaims, misses a data-quality issue, or generates weak evidence.

## Development Setup

This repository is a Codex skill, so there is no build step.

To use it locally:

```bash
mkdir -p ~/.codex/skills
cp -R . ~/.codex/skills/eda
```

Validate the skill if you have the Codex skill creator scripts installed:

```bash
python3 ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ~/.codex/skills/eda
```

## Pull Requests

1. Keep changes scoped.
2. Update `references/report-contract.md` when you change the report interface.
3. Avoid adding generated outputs unless they are useful as documentation assets.
4. Do not commit private datasets, API keys, or proprietary spreadsheets.

## Commit Style

Use clear conventional-style prefixes when practical:

- `docs:` documentation changes
- `feat:` new skill capability
- `fix:` behavior correction
- `chore:` repository maintenance

## License

By contributing, you agree that your contributions are licensed under the MIT License.
