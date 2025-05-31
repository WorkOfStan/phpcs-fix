# PHPCS-Fix

**PHP Code Beautifier and Fixer** is a GitHub Action that checks your PHP code for style issues using the supported [PHPCSStandards/PHP_CodeSniffer](https://github.com/PHPCSStandards/PHP_CodeSniffer/) and automatically attempts to fix them using phpcbf ensuring compatibility with [Super-Linter](https://github.com/super-linter/super-linter).
(Note that the original [squizlabs/PHP_CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer) was abandoned.)
This action can either commit changes directly to the current branch or create a new branch if you prefer manual review via pull request.

## Features

- **Automatic Fixes:** Check PHP files for coding standard violations and attempts to automatically fix them when possible.
- **Flexible Committing:** Creates a new branch (prefixed with `phpcbf/fix`) if changes are required, making it easy to review and merge. Or you can set the input parameter `commit-changes: true` to commit to the current branch. (Recommendation: start with committing to another branch and review in order to avoid surprises. Then commit to the same branch for incremental changes.)
- **Configurable:** Adjust extensions, ignore and standard parameters of phpcs. Adjust PHP version, or commit-message.
- **Caching:** Cache `vendor/` (for a unique combination of php-version and composer.json) after a successful run in order to speed up further runs.
- **GitHub Action Chain:** Either leave this GitHub Action non-blocking or set `stop-on-manual-fix` according to your automation process needs.
- **Direct link to a new commit:** A URL to the new commit is displayed as a notice to allow for a quick check of what's been changed.

## Usage

This action is designed to be used as a _reusable workflow_. You can call it from another workflow in your repository or simply add [the provided YAML configuration](.github/workflows/phpcs-phpcbf.yml) to your repository.

By default, if necessary, a new branch with a name starting with `phpcbf/fix` will be created, making it easy to review and merge fixes into your main branch.

Note 1: This action is non-blocking, so the status remains green even when changes are proposed via a new branch.
A notice message is displayed, and it is then up to you to either create a pull request to merge the changes or delete the branch.

Note 2: However, you can mandate the action to stop if manual fixes are necessary by setting the input parameter `stop-on-manual-fix: true`.

### Permissions

This action requires the following permissions:

```yaml
permissions:
  contents: write
```

### Inputs (all optional)

| Input                | Description                                                                                                                  | Type    | Default                                                                                                                                                                                                                                                                                              |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `commit-changes`     | If set to `true`, the action will commit changes to the current branch; otherwise a new branch is created for manual review. | Boolean | `false`                                                                                                                                                                                                                                                                                              |
| `commit-message`     | Commit message to use if the action commits changes.                                                                         | String  | `"PHP Code Beautifier fixes applied automatically"`                                                                                                                                                                                                                                                  |
| `debug`              | Enable extra debug output (list of branches).                                                                                | Boolean | `false`                                                                                                                                                                                                                                                                                              |
| `extensions`         | Comma-delimited list of file extensions to be sniffed. Note: an empty value will disable checking.                           | String  | `"php"` (defaults to PHP only; other file types must be specified)                                                                                                                                                                                                                                   |
| `ignore`             | Ignore files based on a comma-separated list of patterns matching files and/or directories.                                  | String  | `vendor/`                                                                                                                                                                                                                                                                                            |
| `php-version`        | The PHP version to use, e.g. `"8.2"`.                                                                                        | String  | `"8.2"`                                                                                                                                                                                                                                                                                              |
| `standard`           | The name of, or the path to, the coding standard to use. Can be a comma-separated list specifying multiple standards.        | String  | The project's `.github/linters/phpcs.xml` (where Super-linter expects it) with fallback to <https://github.com/super-linter/super-linter/blob/main/TEMPLATES/phpcs.xml> copied to [.github/linters/super-linter-templates-phpcs.xml](.github/linters/super-linter-templates-phpcs.xml) will be used. |
| `stop-on-manual-fix` | If true, the execution will stop when manual fixes are necessary.                                                            | Boolean | `false`                                                                                                                                                                                                                                                                                              |

### Outputs

| Output          | Description                                     |
| --------------- | ----------------------------------------------- |
| `branch-name`   | The name of the branch created/used             |
| `changed-files` | Comma-separated list of files changed by phpcbf |

## Caching Mechanism

To optimize execution time, the `vendor` folder is cached, allowing dependencies to be reused across workflow runs. The cache key is generated based on:

- `composer.json` – to track dependency changes.
- The runner's OS and PHP version – to account for environment-specific variations.

This approach enables cache sharing across branches. However, if the `composer.json` file in the referenced branch (e.g., `dev`) changes, it's recommended to **invalidate the cache** to ensure a fresh `vendor` folder is built from scratch.

The cache name (key) is `phpcs-fix-${{ runner.os }}-PHP${{ inputs.php-version }}-vendor-${{ hashFiles('**/composer.json') }}`
