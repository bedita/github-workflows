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
    #### to use specific phpcs-standard, uncomment the following 2 lines
    # with:
    #   phpcs-standard: "vendor/cakephp/cakephp-codesniffer/CakePHP"
```