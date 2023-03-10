# Git ref matrix action

[![GitHub release](https://img.shields.io/github/release/cytopia/git-ref-matrix-action.svg?logo=github)](https://github.com/cytopia/git-ref-matrix-action/releases/latest)
[![GitHub marketplace](https://img.shields.io/badge/marketplace-git--ref--matrix--action-blue?logo=github)](https://github.com/marketplace/actions/git-ref-matrix-action)
[![](https://img.shields.io/badge/github-cytopia%2Fgit--ref--matrix--action-red.svg?logo=github)](https://github.com/cytopia/git-ref-matrix-action "github.com/cytopia/git-ref-matrix-action")

[![test-with](https://github.com/cytopia/git-ref-matrix-action/actions/workflows/test-with-refs.yml/badge.svg)](https://github.com/cytopia/git-ref-matrix-action/actions/workflows/test-with-refs.yml)
[![test-disabled](https://github.com/cytopia/git-ref-matrix-action/actions/workflows/test-disabled.yml/badge.svg)](https://github.com/cytopia/git-ref-matrix-action/actions/workflows/test-disabled.yml)
[![test-without](https://github.com/cytopia/git-ref-matrix-action/actions/workflows/test-without-refs.yml/badge.svg)](https://github.com/cytopia/git-ref-matrix-action/actions/workflows/test-without-refs.yml)

This composite action creates a stringified JSON list of git refs to be used as a build matrix.


## :arrow_forward: Inputs

The following inputs can be used to alter the Docker tag name determination:

| Input                          | Type    | Required | Default   | Description                                                                                           |
|--------------------------------|---------|----------|-----------|-------------------------------------------------------------------------------------------------------|
| `repository_default_branch`    | string  | No       | `master`  | Optionally to fetch x number of latest tags from repository's main branch to add to the build matrix. |
| `branches`                     | string  | No       | ``        | Optionally specify a comma separated list of branches to add to the build matrix.                     |
| `tags`                         | string  | No       | ``        | Optionally specify a comma separated list of tags to add to the build matrix.                         |
| `num_latest_tags`              | number  | No       | `0`       | Optionally add x number of latest git tags to the build matrix (requires `repository_default_branch` to point to your repository's main branch. |
| `disable_refs`                 | string  | No       | `'false'` | Optionally force to disable all refs alltogether by returning an empty matrix. Values: `'0'`, `'1'`, `'false'`, `'true'`<br/><strong>Note:</strong> This is a string value, not a boolean. |


## :arrow_backward: Outputs

| Output       | Type    | Description                                                                     |
|--------------|---------|---------------------------------------------------------------------------------|
| `matrix`     | string  | Stringified JSON build matrix. Example: `["master","v0.1.0"]`                   |
| `has_refs`   | string  | String that tells if we have matrix refs or not. Example: `'true'` or `'false'`<br/><strong>Note:</strong> This is a string value, not a boolean. |


## :computer: Usage


```yaml
on: [push]

jobs:
  get-matrix:
    runs-on: ubuntu-latest
    name: Get matrix
    outputs:
      matrix_ref: ${{ steps.set-matrix.outputs.matrix }}
    steps:
      - name: Fetch matrix
        id: set-matrix
        uses: cytopia/git-ref-matrix-action@v0.1.0
        with:
          repository_default_branch: master
          branches: master
          tags: v0.1.0,v0.1.1

  build:
    needs: [get-matrix]
    runs-on: ubuntu-latest
    name: build
    strategy:
      fail-fast: false
      matrix:
        ref:
          - ${{ steps.set-matrix.outputs.matrix }}
    steps:
      - name: "[SETUP] Checkout repository (ref: ${{ matrix.refs }})"
        uses: actions/checkout@v2
        with:
          fetch-depth: 0
          ref: ${{ matrix.refs }}
      - name: Show git
        run: |
          git log | head -20
```


## :exclamation: Keep up-to-date with GitHub Dependabot

Since [Dependabot](https://docs.github.com/en/github/administering-a-repository/keeping-your-actions-up-to-date-with-github-dependabot) has [native GitHub Actions support](https://docs.github.com/en/github/administering-a-repository/configuration-options-for-dependency-updates#package-ecosystem), to enable it on your GitHub repo all you need to do is add the `.github/dependabot.yml` file:

```yml
version: 2
updates:
  # Maintain dependencies for GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "daily"
```


## :octocat: [cytopia](https://github.com/cytopia) GitHub Actions

| Name                             | Description |
|----------------------------------|-------------|
| [docker-tag-action]              | Determines Docker tags based on git branch, commit or git tag |
| [git-ref-matrix-action]          | Create stringified JSON list of git refs to be used as a build matrix |
| [shell-command-retry-action]     | Retries shell commands to avoid failing pipelines due to network issues |
| [upload-artifact-verify-action]  | Upload artifact and verifies it by downloading it again |
| [upload-artifact-retry-action]   | Retries `upload-artifact-verify-action` |
| [download-artifact-retry-action] | Download artifact with retry functionality |

[docker-tag-action]: https://github.com/cytopia/docker-tag-action
[git-ref-matrix-action]: https://github.com/cytopia/git-ref-matrix-action
[shell-command-retry-action]: https://github.com/cytopia/shell-command-retry-action
[upload-artifact-verify-action]: https://github.com/cytopia/upload-artifact-verify-action
[upload-artifact-retry-action]: https://github.com/cytopia/upload-artifact-retry-action
[download-artifact-retry-action]: https://github.com/cytopia/download-artifact-retry-action


## :page_facing_up: License

**[MIT License](LICENSE)**

Copyright (c) 2022 [cytopia](https://github.com/cytopia)
