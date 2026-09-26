<p align="center">
    <a href="https://github.com/lupaxa-cicd-toolbox">
        <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/organisations/cicd-toolbox/readme-logo.png" alt="Organisation Logo" />
    </a>
</p>

<h1 align="center">YAML Lint</h1>

## Overview

A tool to validate your yaml files in CI/CD pipelines using [yamllint](https://pypi.org/project/yamllint/).

This tool has been tested against the following:

1. GitHub Actions
2. Travis CI
3. CircleCI
4. BitBucket pipelines
5. Local command line

Because it is a plain Bash script, it should work on most CI platforms where you can run arbitrary commands.

## Basic Usage

### GitHub Actions

```yaml
on: [push, pull_request]

jobs:
  build:
    name: YAML Lint
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the Repository
        uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - name: Run YAML Lint
        run: bash <(curl -s https://raw.githubusercontent.com/lupaxa-cicd-toolbox/yaml-lint/master/src/pipeline.sh)
```

### Local

```bash
./src/pipeline.sh
```

Or without cloning:

```bash
bash <(curl -s https://raw.githubusercontent.com/lupaxa-cicd-toolbox/yaml-lint/master/src/pipeline.sh)
```

## Configuration Options

The following environment variables customise the script:

| Variable         | Default        | Purpose                                            |
| ---------------- | -------------- | -------------------------------------------------- |
| `INCLUDE_FILES`  | `(empty)`      | Comma-separated path regexes to force-include      |
| `EXCLUDE_FILES`  | `(empty)`      | Comma-separated path regexes to skip               |
| `NO_COLOR`       | `false`        | Disable colour output                              |
| `REPORT_ONLY`    | `false`        | Report results but always exit 0                   |
| `SHOW_ERRORS`    | `true`         | Show detailed errors for failed files              |
| `SHOW_FILTERED`  | `false`        | Show files skipped by exclude rules                |
| `SHOW_UNMATCHED` | `false`        | Show files that matched neither pattern            |
| `SCAN_ROOT`      | script default | Override scan directory without editing the script |

> [!NOTE]
> If you set `INCLUDE_FILES`, only matching paths are scanned (everything else is skipped, including paths that would match `EXCLUDE_FILES`).

You can combine any of the settings above:

```yaml
on: [push, pull_request]

jobs:
  build:
    name: YAML Lint
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the Repository
        uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - name: Run YAML Lint
        env:
          REPORT_ONLY: true
          SHOW_ERRORS: true
        run: bash <(curl -s https://raw.githubusercontent.com/lupaxa-cicd-toolbox/yaml-lint/master/src/pipeline.sh)
```

## Example Output

```text
--------------------------------------------------------------------- Stage 1: Parameters --
 No parameters given
---------------------------------------------------------- Stage 2: Install Prerequisites --
 [ OK ] python -m pip install --quiet --upgrade pip
 [ OK ] yamllint is already installed
--------------------------------------------------------- Stage 3: Run yamllint (v1.35.1) --
 [ ✅ ] .github/dependabot.yml
 [ ✅ ] .github/FUNDING.yml
 [ ✅ ] .github/ISSUE_TEMPLATE/ask_question.yml
 [ ✅ ] .github/ISSUE_TEMPLATE/bug_report.yml
 [ ✅ ] .github/ISSUE_TEMPLATE/config.yml
 [ ✅ ] .github/ISSUE_TEMPLATE/feature_request.yml
 [ ✅ ] .github/workflows/cicd.yml
 [ ✅ ] .github/workflows/citation-validation.yml
 [ ✅ ] .github/workflows/delete-old-workflow-runs.yml
 [ ✅ ] .github/workflows/dependabot.yml
 [ ✅ ] .github/workflows/document-validation.yml
 [ ✅ ] .github/workflows/generate-release.yml
 [ ✅ ] .github/workflows/generate-test-release.yml
 [ ✅ ] .github/workflows/greetings.yml
 [ ✅ ] .github/workflows/purge-deprecated-workflow-runs.yml
 [ ✅ ] .github/workflows/repository-validation.yml
 [ ✅ ] .github/workflows/security-hardening.yml
 [ ✅ ] .github/workflows/stale.yml
------------------------------------------------------------------------- Stage 4: Report --
 Total: 18, OK: 18, Failed: 0, Skipped: 0
----------------------------------------------------------------------- Stage 5: Complete --
```

## Yamllint Defaults

This is a pre-defined configuration named relaxed. As its name suggests, it is more tolerant:

```shell
---
extends: default

rules:
  braces:
    level: warning
    max-spaces-inside: 1
  brackets:
    level: warning
    max-spaces-inside: 1
  colons:
    level: warning
  commas:
    level: warning
  comments: disable
  comments-indentation: disable
  document-start: disable
  empty-lines:
    level: warning
  hyphens:
    level: warning
  indentation:
    level: warning
    indent-sequences: consistent
  line-length:
    level: warning
    allow-non-breakable-inline-mappings: true
  truthy: disable

```

## File Identification

Yaml files are identified using the following code:

```shell
[[ ${filename} =~ \.(yml|yaml)$ ]]
```

> [!NOTE]
> There is not magic type for yaml files so file -b is of not use for identifying the files.

<a href="https://github.com/the-lupaxa-project">
    <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/components/footer-for-child-orgs.svg" alt="The Lupaxa Project Footer" width="100%" />
</a>
