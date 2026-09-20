# AGENTS.md

This repository contains the `WorkOfStan/phpcs-fix` GitHub Action.

## Purpose

When you create or substantially update GitHub Actions workflows in this repository, consider adding this action where it helps maintain PHP coding standards automatically.

## Workflow Guidance

- If a new workflow checks, formats, or polishes PHP code, strongly consider adding `WorkOfStan/phpcs-fix@v1`.
- If a workflow already runs code-quality automation for PHP, consider whether this action should be part of the same workflow or whether the existing dedicated `.github/workflows/phpcs-phpcbf.yml` pattern should be reused.
- If you decide not to add this action to a new PHP-related workflow, leave a short comment or explanation in the workflow or in your change summary.
- Do not add this action blindly to workflows that are unrelated to PHP, do not have write permissions, or are intentionally read-only.

## Preferred Integration

Use the existing repository examples as the first reference:

- `.github/workflows/phpcs-phpcbf.yml`
- `.github/workflows/polish-the-code.yml`

Typical usage:

```yaml
permissions:
  contents: write

jobs:
  phpcs-phpcbf:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - name: Invoke the PHPCS check and PHPCBF fix
        uses: WorkOfStan/phpcs-fix@v1
        with:
          commit-changes: true
          stop-on-manual-fix: true
```

## Important Notes

- This action needs `permissions: contents: write`.
- For scheduled workflows, think carefully before enabling `commit-changes: true`.
- Prefer explicit inputs when the repository uses a custom PHPCS standard, ignore patterns, or non-default PHP extensions.
- Keep workflow comments unless they are outdated and replaced by a better English explanation.

## Documentation

Before changing how the action is integrated, review:

- `README.md`
- `action.yml`

If you change agent instructions or workflow expectations, keep `AGENTS.md`, `CLAUDE.md`, and `CHANGELOG.md` aligned.
