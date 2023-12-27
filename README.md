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
  php_cs_stan_unit:
    uses: bedita/github-workflows/.github/workflows/php-cs-stan-unit.yml@v1
```

## php-cs.yml

Phpcs checks on specified php versions.

Usage:

```yaml
name: 'cs'

on:
  pull_request:
    paths:
      - '**/*.php'
      - '.github/workflows/cs.yml'
      - 'composer.json'
  push:
    paths:
      - '**/*.php'
      - '.github/workflows/cs.yml'
      - 'composer.json'

jobs:
  cs:
    uses: bedita/github-workflows/.github/workflows/php-cs.yml@v1
    with:
      php_versions: '["7.4", "8.0", "8.1"]'
```

## php-psalm.yml

Psalm checks on specified php versions.

Usage:

```yaml
name: 'psalm'

on:
  pull_request:
    paths:
      - '**/*.php'
      - '.github/workflows/psalm.yml'
      - 'composer.json'
  push:
    paths:
      - '**/*.php'
      - '.github/workflows/psalm.yml'
      - 'composer.json'

jobs:
  psalm:
    uses: bedita/github-workflows/.github/workflows/php-psalm.yml@v1
    with:
      php_versions: '["7.4", "8.0", "8.1"]'
```

## php-stan.yml

Phpstan checks on specified php versions.

Usage:

```yaml
name: 'stan'

on:
  pull_request:
    paths:
      - '**/*.php'
      - '.github/workflows/stan.yml'
      - 'composer.json'
  push:
    paths:
      - '**/*.php'
      - '.github/workflows/stan.yml'
      - 'composer.json'

jobs:
  stan:
    uses: bedita/github-workflows/.github/workflows/php-stan.yml@v1
    with:
      php_versions: '["7.4", "8.0", "8.1"]'
```

## php-unit.yml

Phpunit checks on specified php versions and bedita versions.
Parameter `coverage_min_percentage` can be used to specify minimum required coverage to pass tests (default is 85).

Usage:

```yaml
name: 'unit'

on:
  pull_request:
    paths:
      - '**/*.php'
      - '.github/workflows/unit.yml'
      - 'composer.json'
  push:
    paths:
      - '**/*.php'
      - '.github/workflows/unit.yml'
      - 'composer.json'

jobs:
  unit-4:
    uses: bedita/github-workflows/.github/workflows/php-unit.yml@v1
    with:
      php_versions: '["7.4"]'
      bedita_version: '4.7.1'
      coverage_min_percentage: 70

  unit-5:
    uses: bedita/github-workflows/.github/workflows/php-unit.yml@v1
    with:
      php_versions: '["7.4","8.0","8.1"]'
      bedita_version: '5.0.0'
      coverage_min_percentage: 70
```

## python-cs.yml

Python styles and linting checks on specified python versions.

Usage:

```yaml
name: 'cs'

on:
  pull_request:
    paths:
      - '**/*.py'
      - '.github/workflows/cs.yml'
  push:
    paths:
      - '**/*.php'
      - '.github/workflows/cs.yml'

jobs:
  cs:
    uses: bedita/github-workflows/.github/workflows/python-cs.yml@v1
    with:
      python_versions: '["3.10", "3.11"]'
```

## python-unit.yml

Python unit tests checks on specified python versions.

Usage:

```yaml
name: 'unit'

on:
  pull_request:
    paths:
      - '**/*.py'
      - '.github/workflows/unit.yml'
  push:
    paths:
      - '**/*.py'
      - '.github/workflows/unit.yml'

jobs:
  unit:
    uses: bedita/github-workflows/.github/workflows/python-unit.yml@v1
    with:
      python_versions: '["3.10","3.11"]'
```


## release.yml

Reusable workflow to create releases via Pull Requests merge, when PRs use labels `release:major`, `release:minor`, `release:patch`.

Input parameters:

* `main_branch` - (mandatory) name of the branch where a new major version is allowed, in other branches major version creation will fail
* `dist_branches` - (mandatory, array in JSON format) name of the branches where a new automatic releae will be created
* `version_bump` - (optional) type of version bump requested (`patch`, `minor`, `major`) or leave blank (empty string) to automatically infer from Pull Request labels
* `version_ini_path` - (optional) path of a version file in `.ini` format where the new version should be saved, this file will be pushed to the working branch
* `version_ini_prefix` - (optional) content of version `.ini` file where the new version will be appended
* `toml_conf_path` - (optional) path of a version file in `.toml` format where the new version should be saved, this file will be pushed to the working branch
* `toml_conf_version_key` - (optional) content of version `.toml` file where the new version will be appended
* `package_json_version` - (optional) boolean flag: true when you want to apply a package.json file version update

Usage example of caller workflow:

```yaml
name: 'release'

on:
  pull_request_target:
    types: [closed]
  workflow_dispatch:
    inputs:
      releaseType:
        description: 'Release type'
        required: true
        default: 'patch'
        type: choice
        options: [patch, minor, major]

jobs:

  release_job:
    uses: bedita/github-workflows/.github/workflows/release.yml@v1
    with:
      main_branch: 'master'
      dist_branches: '["master", "1.x"]'
      version_bump: ${{ inputs.releaseType }}
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
