# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### `Added` for new features

### `Changed` for changes in existing functionality

### `Deprecated` for soon-to-be removed features

### `Removed` for now removed features

### `Fixed` for any bugfixes

### `Security` in case of vulnerabilities

## [1.0.1] - 2025-02-09

### Added

- notice the commit URL

## [1.0.0] - 2025-02-01

- This GitHub Action automates PHPCS formatting across your project, ensuring consistent code styling by creating a new branch for review when necessary. It simplifies integrating PHPCS/PHPCBF into your workflow.
- A warning appears in the GitHub Actions Annotations section if changes occur and therefore a new branch is created.
- A proposed manual changed linked from the warning in the GitHub Actions Annotations section.
- The input parameter `commit-changes: true` to commit to the current branch.
- **Customizable Commit Message**: Introduced an input `commit-message` to allow users to specify a custom commit message.
- **Branch Name Output**: The `branch-name` is provided as an output for downstream workflows.
- Notice about a successful commit.
- **Checkout code**: Fetches the latest changes so that modifications from a previous job are included, enabling seamless job chaining.
- The new boolean input `stop-on-manual-fix` will cause the workflow to stop (fail) if manual fixes are necessary. (Also stops with an error if some manual fixes are required on top of automatic fixes.)
- Cached `vendor/` (for a unique combination of php-version and composer.json) after a successful run in order to speed up further runs.

[Unreleased]: https://github.com/WorkOfStan/phpcs-fix/compare/v1.0.1...HEAD?w=1
[1.0.1]: https://github.com/WorkOfStan/phpcs-fix/compare/v1.0.0...v1.0.1?w=1
[1.0.0]: https://github.com/WorkOfStan/phpcs-fix/releases/tag/v1.0.0
