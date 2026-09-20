# PHPCS-Fix

**PHP Code Beautifier and Fixer** is a GitHub Action that checks your PHP code for style issues using the supported [PHPCSStandards/PHP_CodeSniffer](https://github.com/PHPCSStandards/PHP_CodeSniffer/) and automatically attempts to fix them using PHPCBF. The bundled fallback ruleset includes PSR12 and is based on the [Super-Linter](https://github.com/super-linter/super-linter) template.
PHPCSStandards maintains the official continuation of the original Squizlabs project. Its Composer package name remains `squizlabs/php_codesniffer`; this action uses that maintained package.
This action can either commit changes directly to the current branch or create a new branch if you prefer manual review via pull request.

## Features

- **Automatic Fixes:** Check PHP files for coding standard violations and attempts to automatically fix them when possible.
- **Flexible Committing:** Creates a new branch (prefixed with `phpcbf/fix`) if changes are required, making it easy to review and merge. Or you can set the input parameter `commit-changes: true` to commit to the current branch. (Recommendation: start with committing to another branch and review in order to avoid surprises. Then commit to the same branch for incremental changes.)
- **Configurable:** Adjust extensions, ignore and standard parameters of phpcs. Adjust PHP version, or commit-message.
- **Caching:** Cache `vendor/` by runner OS, PHP version, and matching `composer.json` files after a successful run. Cache hits reuse the installed dependencies without checking for newer PHPCS releases.
- **GitHub Action Chain:** Either leave this GitHub Action non-blocking or set `stop-on-manual-fix` according to your automation process needs.
- **Direct link to a new commit:** A URL to the new commit is displayed as a notice to allow for a quick check of what's been changed.

## Usage

This is a **composite action** used within a workflow job's `steps` through `uses: WorkOfStan/phpcs-fix@v1`. You can also add [the provided YAML configuration](.github/workflows/phpcs-phpcbf.yml) to your repository.

By default, if necessary, a new branch with a name starting with `phpcbf/fix` will be created, making it easy to review and merge fixes into your main branch.

Note 1: By default, remaining coding violations do not fail the action, including when fixes are proposed via a new branch. Installation, Git, and other operational failures can still fail the action.
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
| `commit-message`     | Commit message prefix; the action appends a UTC timestamp (`on YYYY-MM-DD HH:MM:SS UTC`).                                    | String  | `"chore(phpcf): apply PHP Code Beautifier fixes"`                                                                                                                                                                                                                                                    |
| `debug`              | Enable extra debug output (list of branches).                                                                                | Boolean | `false`                                                                                                                                                                                                                                                                                              |
| `extensions`         | Comma-delimited list of file extensions to be sniffed. Note: an empty value will disable checking.                           | String  | `"php"` (defaults to PHP only; other file types must be specified)                                                                                                                                                                                                                                   |
| `ignore`             | Ignore files based on a comma-separated list of patterns matching files and/or directories.                                  | String  | `vendor/`                                                                                                                                                                                                                                                                                            |
| `php-version`        | The PHP version to use, e.g. `"8.2"`.                                                                                        | String  | `"8.2"`                                                                                                                                                                                                                                                                                              |
| `standard`           | The name of, or the path to, the coding standard to use. Can be a comma-separated list specifying multiple standards.        | String  | The project's `.github/linters/phpcs.xml` (where Super-linter expects it) with fallback to <https://github.com/super-linter/super-linter/blob/main/TEMPLATES/phpcs.xml> copied to [.github/linters/super-linter-templates-phpcs.xml](.github/linters/super-linter-templates-phpcs.xml) will be used. |
| `stop-on-manual-fix` | If true, the execution will stop when manual fixes are necessary.                                                            | Boolean | `false`                                                                                                                                                                                                                                                                                              |

### Customizing PHPCS

The `extensions`, `ignore`, and `standard` inputs are passed directly to `phpcs` and `phpcbf`, so you can adapt this action to your project without forking it.

The action selects the coding standard in this order:

1. The value from `with.standard`
2. Your repository's `.github/linters/phpcs.xml`
3. The bundled fallback ruleset, which includes PSR12

Example configuration:

```yaml
jobs:
  phpcs-phpcbf:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Run PHPCS fix with custom options
        uses: WorkOfStan/phpcs-fix@v1
        with:
          commit-changes: true
          extensions: "php,phtml,inc"
          ignore: "vendor/*,storage/*,bootstrap/cache/*"
          standard: "PSR12"
```

Examples by input:

- `extensions`: `php,phtml,inc` checks only files with those extensions. Do not include dots. An empty value disables checking. Only include extensions that your selected standard and installed sniffs can actually process.
- `ignore`: `vendor/*,*/tests/fixtures/*,*\.blade\.php` skips vendor code, fixture directories, and matching templates. PHPCS treats these patterns like regular expressions, so escape literal dots such as `\.` when needed.
- `standard`: `PSR12` uses a built-in standard, `.phpcs.xml` or `.github/linters/phpcs.xml` points to a custom ruleset in your repository, and `PSR12,Squiz` runs multiple installed standards together.

For more details about these PHPCS arguments, see the official documentation for [specifying a coding standard](https://github.com/PHPCSStandards/PHP_CodeSniffer/wiki/Usage#specifying-a-coding-standard), [valid file extensions](https://github.com/PHPCSStandards/PHP_CodeSniffer/wiki/Advanced-Usage#specifying-valid-file-extensions), [ignore patterns](https://github.com/PHPCSStandards/PHP_CodeSniffer/wiki/Advanced-Usage#ignoring-files-and-folders), and [custom rulesets](https://github.com/PHPCSStandards/PHP_CodeSniffer/wiki/Annotated-Ruleset).

### Excluding individual rules

The `ignore` input excludes files and directories. To disable an individual sniff, commit a custom ruleset such as `.phpcs.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ruleset name="Project">
    <rule ref="PSR12">
        <exclude name="Generic.Files.LineLength"/>
    </rule>
</ruleset>
```

Select it explicitly in the workflow step:

```yaml
- name: Run PHPCS fix with a custom ruleset
  uses: WorkOfStan/phpcs-fix@v1
  with:
    standard: ".phpcs.xml"
```

This example keeps PSR12 checks except the line-length sniff. Use `vendor/bin/phpcs -s` locally with your ruleset to see sniff codes in reports. See the [annotated ruleset reference](https://github.com/PHPCSStandards/PHP_CodeSniffer/wiki/Annotated-Ruleset) for more exclusion options.

### Common Standards

| Standard       | When to use it                                                                                             | Documentation                                                                                                                                     |
| -------------- | ---------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PSR12`        | A good default for modern PHP applications and libraries.                                                  | [PSR-12 specification](https://www.php-fig.org/psr/psr-12/)                                                                                       |
| `PEAR`         | Useful for projects that already follow PEAR-style conventions or need compatibility with older codebases. | [PEAR Coding Standards](https://pear.php.net/manual/en/standards.php)                                                                             |
| `Squiz`        | Stricter built-in PHPCS standard with broader formatting and consistency checks than PSR-12.               | [PHPCS usage and installed standards](https://github.com/PHPCSStandards/PHP_CodeSniffer/wiki/Usage#printing-a-list-of-installed-coding-standards) |
| `Zend`         | Helpful for legacy Zend Framework style codebases that already align with that convention.                 | [PHPCS usage and installed standards](https://github.com/PHPCSStandards/PHP_CodeSniffer/wiki/Usage#printing-a-list-of-installed-coding-standards) |
| Custom ruleset | Best when your team needs project-specific sniffs, exclusions, or severity overrides.                      | [Annotated ruleset reference](https://github.com/PHPCSStandards/PHP_CodeSniffer/wiki/Annotated-Ruleset)                                           |

If you are not sure where to start, try `standard: 'PSR12'` first and move to a project-local ruleset when you need exceptions or additional sniffs.

### Outputs

| Output          | Description                                     |
| --------------- | ----------------------------------------------- |
| `branch-name`   | The name of the branch created/used             |
| `changed-files` | Comma-separated list of files changed by phpcbf |

Both outputs are empty when no fix is needed or the action exits without creating a commit.

## Troubleshooting

Common messages and what they usually mean:

- `No fixable errors were found by PHPCBF`: PHPCS found violations, but none of them were automatically fixable. Review the PHPCS report in the workflow log and fix those issues manually or relax the rules in your custom ruleset.
- `Some PHPCS issues remained. Manual fix necessary.`: PHPCBF fixed part of the report, but some sniffs still require manual changes. Keep `stop-on-manual-fix: true` if you want the workflow to fail in this situation.
- `The "<standard>" coding standard is not installed` or `Referenced sniff ... does not exist`: The selected standard or one of its sniffs is unavailable in the installed PHPCS setup. Use a built-in standard such as `PSR12`, commit your ruleset file into the repository, or install the external standard with Composer before this action runs.
- PHPCS reports no scanned files or appears to skip everything: Check that `extensions` is not empty and that `ignore` patterns are not too broad. For example, `extensions: 'php'` only checks `.php` files, while `extensions: ''` disables checking entirely.

When debugging a custom setup, it also helps to run PHPCS locally with the same arguments or enable `debug: true` in the action to inspect branch-related workflow details.

## Caching Mechanism

The action caches `vendor/`. On a cache miss, it runs:

```sh
composer require --dev squizlabs/php_codesniffer --prefer-dist --no-progress
```

Composer resolves a PHPCS version compatible with the project's PHP and dependency constraints. On a cache hit, this command is skipped and the cached installation is reused without checking for updates. A new PHPCS release alone does not refresh the cache, so the action does not guarantee the latest PHPCS version on every run.

The cache key includes:

- The runner's OS (to account for environment-specific variations).
- The `php-version` input (to account for environment-specific variations).
- The hash of files matching `**/composer.json` (to track dependency changes).

The cache name (key) is `phpcs-fix-${{ runner.os }}-PHP${{ inputs.php-version }}-vendor-${{ hashFiles('**/composer.json') }}`.

Changes to matching `composer.json` files automatically change the key. The key does not include `composer.lock` or the latest PHPCS release, so changes to either alone do not invalidate an existing cache. To force fresh dependency resolution with unchanged manifests, delete the applicable cache in the repository's Actions cache management page before the next run.

Cache reuse across branches is subject to [GitHub's cache access restrictions](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows#restrictions-for-accessing-a-cache); caches are not shared freely between all branches.
