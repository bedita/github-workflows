# github-workflows

## php-cs-stan-unit.yml

Reusable workflow that launches on an ubuntu-latest phpcs, phpstan and phpunit (with php 7.4, 8.0 and 8.1) checks.

Usage example of caller workflow:

```yaml
name: 'php'

on:
  pull_request:
    paths:
      - '**/*.php'
      - '.github/workflows/php.yml'
      - 'composer.json'
  push:
    paths:
      - '**/*.php'
      - '.github/workflows/php.yml'
      - 'composer.json'

jobs:
  call:
    uses: bedita/github-workflows/.github/workflows/php-cs-stan-unit.yml@main
```

## release.yml

Reusable workflow to create releases via Pull Requests merge, when PRs use labels `release:major`, `release:minor`, `release:patch`.

Input parameters:

* `main_branch` - (mandatory) name of the branch where a new major version is allowed, in other branches major version creation will fail
* `dist_branches` - (mandatory, array in JSON format) name of the branches where a new automatic releae will be created
* `version_ini_path` - (optional) path of a version file in `.ini` format where the new version should be saved, this file will be pushed to the working branch
* `version_ini_prefix` - (optional) content of version `.ini` file where the new version will be appended

Usage example of caller workflow:

```yaml
name: 'release'

on:
  pull_request:
    types: [closed]

jobs:

  release_job:
    uses: bedita/github-workflows/.github/workflows/release.yml@main
    with:
      main_branch: 'master'
      dist_branches: '["master", "1.x"]'
      version_ini_path: config/version.ini
      version_ini_prefix: "[MyApp]\nversion = "

  other_job:
    runs-on: 'ubuntu-latest'
    needs: release_job
    steps:

      - name: Debug output version
        run: |
          echo version var ${{ needs.release_job.outputs.version }}
```

The newly created release version is available in the calling workflow with `${{ needs.<job_id>.outputs.version }}` - where `<job_id>` is the the job ID using this release workflow, that must be included in `needs` section in the caller job.
