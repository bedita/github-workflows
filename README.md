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

Usage example of caller workflow:

```yaml
name: 'release'

on:
  pull_request:
    types: [closed]

jobs:
  call:
    uses: bedita/github-workflows/.github/workflows/release.yml@main
    with:
      main-branch: 'master'
      dist-branches: '["master", "1.x"]'
```
