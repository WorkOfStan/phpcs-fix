# PHPCS-Fix

**PHP Code Beautifier and Fixer** is a GitHub Action that checks your PHP code for style issues using the supported [PHPCSStandards/PHP_CodeSniffer](https://github.com/PHPCSStandards/PHP_CodeSniffer/) and automatically attempts to fix them using phpcbf ensuring compatibility with [Super-Linter](https://github.com/super-linter/super-linter).
(As the original [squizlabs/PHP_CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer) was abandoned.
It can either commit changes directly to the current branch or create a new branch if you prefer manual review via pull request.

## Features

- **Automatic Fixes:** Check PHP files for coding standard violations and attempts to automatically fix them when possible.
- **Flexible Committing:** Creates a new branch (prefixed with `phpcbf/fix`) if changes are required, making it easy to review and merge. Or you can set the input parameter `commit-changes: true` to commit to the current branch. (Recommendation: start with committing to another branch and review in order to avoid surprises. Then commit to the same branch for incremental changes.)
- **Configurable:** Adjust extensions, ignore and standard parameters of phpcs. Adjust PHP version, or commit-message.

## Usage

This action is designed to be used as a _reusable workflow_. You can call it from another workflow in your repository or simply add [the provided YAML configuration](.github/workflows/phpcs-phpcbf.yml) to your repository.

By default, if needed, a new branch with a name starting with `prettier/fix` will be created, making it easy to review and merge the fixes into your main branch.

Note: This action is not blocking, so the status remains green even if changes are proposed in the form of a new branch. Then it’s up to you to either create a pull request to merge the changes or delete the branch.

### Permissions

This action requires the following permissions:

```yaml
permissions:
  contents: write
```

### Inputs (all optional)

| Input            | Description                                                                                                                                                                                                                                                                                                                                                                              | Type    | Default                                                            |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------- | ------------------------------------------------------------------ |
| `commit-changes` | If set to `true`, the action will commit changes to the current branch; otherwise a new branch is created for manual review.                                                                                                                                                                                                                                                             | Boolean | `false`                                                            |
| `commit-message` | Commit message to use if the action commits changes.                                                                                                                                                                                                                                                                                                                                     | String  | `"PHP Code Beautifier fixes applied automatically"`                |
| `extensions`     | Comma-delimited list of file extensions to be sniffed. Note: an empty value will disable checking.                                                                                                                                                                                                                                                                                       | String  | `"php"` (defaults to PHP only; other file types must be specified) |
| `ignore`         | Ignore files based on a comma-separated list of patterns matching files and/or directories. Default: `vendor/`                                                                                                                                                                                                                                                                           | String  |
| `php-version`    | The PHP version to use, e.g. `"8.2"`.                                                                                                                                                                                                                                                                                                                                                    | String  | `"8.2"`                                                            |
| `standard`       | The name of, or the path to, the coding standard to use. Can be a comma-separated list specifying multiple standards. If not set, the project's `.github/linters/phpcs.xml` or <https://github.com/super-linter/super-linter/blob/main/TEMPLATES/phpcs.xml> copied to [.github/linters/super-linter-templates-phpcs.xml](.github/linters/super-linter-templates-phpcs.xml) will be used. | String  |

### Outputs

| Output        | Description                         |
| ------------- | ----------------------------------- |
| `branch-name` | The name of the branch created/used |
