[![Codacy Badge](https://app.codacy.com/project/badge/Grade/4a625e9b62794b5b98e169c15c0e673c)](https://www.codacy.com/gh/tj-actions/changed-files/dashboard?utm_source=github.com\&utm_medium=referral\&utm_content=tj-actions/changed-files\&utm_campaign=Badge_Grade)
[![CI](https://github.com/tj-actions/changed-files/actions/workflows/test.yml/badge.svg)](https://github.com/tj-actions/changed-files/actions/workflows/test.yml) [![Update release version.](https://github.com/tj-actions/changed-files/actions/workflows/sync-release-version.yml/badge.svg)](https://github.com/tj-actions/changed-files/actions/workflows/sync-release-version.yml) [![Public workflows that use this action.](https://img.shields.io/endpoint?url=https%3A%2F%2Fused-by.vercel.app%2Fapi%2Fgithub-actions%2Fused-by%3Faction%3Dtj-actions%2Fchanged-files%26badge%3Dtrue)](https://github.com/search?o=desc\&q=tj-actions+changed-files+language%3AYAML\&s=\&type=Code)

[![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?logo=ubuntu\&logoColor=white)](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idruns-on)
[![Mac OS](https://img.shields.io/badge/mac%20os-000000?logo=macos\&logoColor=F0F0F0)](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idruns-on)
[![Windows](https://img.shields.io/badge/Windows-0078D6?logo=windows\&logoColor=white)](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idruns-on)


<!-- ALL-CONTRIBUTORS-BADGE:START - Do not remove or modify this section -->
 
[![All Contributors](https://img.shields.io/badge/all_contributors-12-orange.svg?style=flat-square)](#contributors-)

<!-- ALL-CONTRIBUTORS-BADGE:END -->

## changed-files

Retrieve all changed files and directories relative to the target branch (`pull_request*` based events) or the last remote commit (`push` based event) returning the **absolute path** to all changed files and directories from the project root.

## Features

*   Fast execution (0-2 seconds on average).
*   Easy to debug.
*   Boolean output indicating that certain files have been changed.
*   Scales to large repositories.
*   Git submodules support.
*   No extra API calls.
*   Monorepos (Fetches only the last remote commit).
*   Supports all platforms (Linux, MacOS, Windows).
*   [GitHub-hosted runners](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners) support
*   [GitHub Enterprise Server](https://docs.github.com/en/enterprise-server@3.3/admin/github-actions/getting-started-with-github-actions-for-your-enterprise/getting-started-with-github-actions-for-github-enterprise-server) support.
*   [self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners) support.
*   List all files and directories that have changed:
    *   Between the current pull request branch and the last commit on the target branch.
    *   Between the current pull request branch and the fork point on the target branch.
    *   Between the last commit and the current pushed change.
    *   Between the last remote branch commit and the current HEAD.
*   Restrict change detection to a subset of files and directories:
    *   Report on files that have at least one change.
    *   Using [Glob pattern](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#filter-pattern-cheat-sheet) matching.

## Usage

> NOTE: :warning:
>
> *   **IMPORTANT:** For `push` events you need to include `fetch-depth: 0` **OR** `fetch-depth: 2` depending on your use case.
> *   When using `persist-credentials: false` with `actions/checkout@v2` you'll need to specify a `token` using the `token` input.
> *   For monorepos where pulling all the branch history might not be desired, you can omit `fetch-depth` for `pull_request` events.
> *   For files located in a sub-directory ensure that the pattern specified contains `**/` (globstar) to match any preceding directories or explicitly pass the full path relative to the project root. See: [#314](https://github.com/tj-actions/changed-files/issues/314).

```yaml
name: CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest  # windows-latest | macos-latest
    name: Test changed-files
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0  # OR "2" -> To retrieve the preceding commit.

      - name: Get changed files
        id: changed-files
        uses: tj-actions/changed-files@v23

      - name: List all changed files
        run: |
          for file in ${{ steps.changed-files.outputs.all_changed_files }}; do
            echo "$file was changed"
          done
```

If you feel generous and want to show some extra appreciation:

Support this project with a :star:

[![Buy me a coffee][buymeacoffee-shield]][buymeacoffee]

[buymeacoffee]: https://www.buymeacoffee.com/jackton1

[buymeacoffee-shield]: https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png

## Outputs

|  Acronym  |   Meaning    |
|:---------:|:------------:|
|     A     |    Added     |
|     C     |    Copied    |
|     M     |   Modified   |
|     D     |   Deleted    |
|     R     |   Renamed    |
|     T     | Type changed |
|     U     |   Unmerged   |
|     X     |   Unknown    |

|               Output               |   type   |                            example                             |                                                                                                      description                                                                                                      |
|:----------------------------------:|:--------:|:--------------------------------------------------------------:|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|            any\_changed            | `string` |                       `true` OR `false`                        |         Returns `true` when any <br /> of the filenames provided using <br /> the `files` input has changed. <br /> i.e. *using a combination of all added, <br />copied, modified and renamed files (ACMR)*          |
|           only\_changed            | `string` |                       `true` OR `false`                        |                                                           Returns `true` when only <br /> files provided using <br /> the `files` input has changed. (ACMR)                                                           |
|       other\_changed\_files        | `string` |                `'new.txt path/to/file.png ...'`                |                                 Select all other changed files <br/> not listed in the files input <br /> i.e. *a  combination of all added, <br /> copied and modified files (ACMR)*                                 |
|           any\_modified            | `string` |                       `true` OR `false`                        | Returns `true` when any <br /> of the filenames provided using <br /> the `files` input has been modified. <br /> i.e. *using a combination of all added, <br />copied, modified, renamed, and deleted files (ACMRD)* |
|           only\_modified           | `string` |                       `true` OR `false`                        |                                                       Returns `true` when only <br /> files provided using <br /> the `files` input has been modified. (ACMRD)                                                        |
|       other\_modified\_files       | `string` |                `'new.txt path/to/file.png ...'`                |                           Select all other modified files <br/> not listed in the files input <br /> i.e. *a  combination of all added, <br /> copied, modified, and deleted files (ACMRD)*                           |
|            any\_deleted            | `string` |                       `true` OR `false`                        |                                                     Returns `true` when any <br /> of the filenames provided using <br /> the `files` input has been deleted. (D)                                                     |
|           only\_deleted            | `string` |                       `true` OR `false`                        |                                                          Returns `true` when only <br /> files provided using <br /> the `files` input has been deleted. (D)                                                          |
|       other\_deleted\_files        | `string` |                `'new.txt path/to/file.png ...'`                |                                               Select all other deleted files <br/> not listed in the files input <br /> i.e. *a  combination of all deleted files (D)*                                                |
|        all\_changed\_files         | `string` |                `'new.txt path/to/file.png ...'`                |                                                  Select all changed files <br /> i.e. *a combination of all added, <br />copied, modified and renamed files (ACMR)*                                                   |
|        all\_modified\_files        | `string` |                `'new.txt path/to/file.png ...'`                |                                             Select all changed files <br /> i.e. *a combination of all added, <br />copied, modified, renamed and deleted files (ACMRD)*                                              |
| all\_changed\_and\_modified\_files | `string` |                `'new.txt path/to/file.png ...'`                |                                                                Select all changed <br /> and modified files <br /> i.e. *a combination of (ACMRDTUX)*                                                                 |
|   all\_old\_new\_renamed\_files    | `string` | `'old name.txt,new name.txt old name 2.txt,new name 2.txt...'` |                                                                        Select only files that are Renamed and list their old and new names (R)                                                                        |
|            added\_files            | `string` |                `'new.txt path/to/file.png ...'`                |                                                                                         Select only files that are Added (A)                                                                                          |
|           copied\_files            | `string` |                `'new.txt path/to/file.png ...'`                |                                                                                         Select only files that are Copied (C)                                                                                         |
|           deleted\_files           | `string` |                `'new.txt path/to/file.png ...'`                |                                                                                        Select only files that are Deleted (D)                                                                                         |
|          modified\_files           | `string` |                `'new.txt path/to/file.png ...'`                |                                                                                        Select only files that are Modified (M)                                                                                        |
|           renamed\_files           | `string` |                `'new.txt path/to/file.png ...'`                |                                                                                        Select only files that are Renamed (R)                                                                                         |
|        type\_changed\_files        | `string` |                `'new.txt path/to/file.png ...'`                |                                                                                Select only files that have their file type changed (T)                                                                                |
|          unmerged\_files           | `string` |                `'new.txt path/to/file.png ...'`                |                                                                                        Select only files that are Unmerged (U)                                                                                        |
|           unknown\_files           | `string` |                `'new.txt path/to/file.png ...'`                |                                                                                        Select only files that are Unknown (X)                                                                                         |

## Inputs

|                 Input                  |          type          | required |        default        |                                                                                                                                                          description                                                                                                                                                          |
|:--------------------------------------:|:----------------------:|:--------:|:---------------------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|                 token                  |        `string`        | `false`  | `${{ github.token }}` | [GITHUB\_TOKEN](https://docs.github.com/en/free-pro-team@latest/actions/reference/authentication-in-a-workflow#using-the-github_token-in-a-workflow) <br /> or a repo scoped <br /> [Personal Access Token](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/creating-a-personal-access-token) |
|               separator                |        `string`        | `false`  |         `' '`         |                                                                                                                                              Split character for output strings                                                                                                                                               |
| include\_all\_old\_new\_renamed\_files |       `boolean`        | `false`  |        `false`        |                                                                                 Include `all_old_new_renamed_files` output. Note this can generate a large debug output See: [#501](https://github.com/tj-actions/changed-files/issues/501).                                                                                  |
|          old\_new\_separator           |        `string`        | `false`  |         `','`         |                                                                                                                                        Split character for old and new filename pairs                                                                                                                                         |
|       old\_new\_files\_separator       |        `string`        | `false`  |         `' '`         |                                                                                                                                    Split character for multiple old and new filename pairs                                                                                                                                    |
|                 files                  | `string` OR `string[]` | `false`  |                       |                                                                                                             Check for changes  <br> using only these <br> list of file(s) <br> (Defaults to the <br> entire repo)                                                                                                             |
|            files\_separator            |        `string`        | `false`  |        `'\n'`         |                                                                                                                                         Separator used to split the<br>`files` input                                                                                                                                          |
|       files\_from\_source\_file        |        `string`        | `false`  |                       |                                                                                                                                  Source file(s) <br> used to populate <br> the `files` input                                                                                                                                  |
|             files\_ignore              |        `string`        | `false`  |                       |                                                                                                                                                Ignore changes to these file(s)                                                                                                                                                |
|        files\_ignore\_separator        |        `string`        | `false`  |        `'\n'`         |                                                                                                                                     Separator used to split the <br>`files-ignore` input                                                                                                                                      |
|   files\_ignore\_from\_source\_file    |        `string`        | `false`  |                       |                                                                                                                              Source file(s) <br> used to populate <br> the `files_ignore` input                                                                                                                               |
|                  sha                   |        `string`        |  `true`  |  `${{ github.sha }}`  |                                                                                                                           Specify a different <br> commit SHA <br> used for <br> comparing changes                                                                                                                            |
|               base\_sha                |        `string`        | `false`  |                       |                                                                                                                         Specify a different <br> base commit SHA <br> used for <br> comparing changes                                                                                                                         |
|                  path                  |        `string`        | `false`  |         `'.'`         |                                                                                                                              Relative path under <br> `GITHUB_WORKSPACE` <br> to the repository                                                                                                                               |
|      since\_last\_remote\_commit       |       `boolean`        | `false`  |        `false`        |       Use the last commit on the remote <br> branch as the `base_sha` <br> (Defaults to the last commit <br> on the target branch for Pull requests <br> or the previous commit <br> on the current branch <br> for push events). <br /> NOTE: This requires <br /> `fetch-depth: 0` <br /> with `actions/checkout@v2`        |
|            use\_fork\_point            |       `boolean`        | `false`  |        `false`        |           Finds best common ancestor <br /> between two commits <br /> to use in a three-way merge <br /> as the `base_sha` <br /> See: [git merge-base](https://git-scm.com/docs/git-merge-base#Documentation/git-merge-base.txt---fork-point). <br> NOTE: This pulls the entire commit history of the base branch           |
|               quotepath                |       `boolean`        | `false`  |        `true`         |                                                                                                                                Output filenames completely verbatim by setting this to `false`                                                                                                                                |
|             diff\_relative             |       `boolean`        | `false`  |                       |                                                                                                                       Exclude changes outside the current directory and show pathnames relative to it.                                                                                                                        |
|               dir\_names               |       `boolean`        | `false`  |        `false`        |                                                                                      Output unique changed directories instead of filenames. <br> NOTE: This returns `.` for <br> changed files located in the root of the project.                                                                                       |

## Examples

```yaml
...
    steps:
      - uses: actions/checkout@v3
#        with:
#          fetch-depth: 0  # OR "2" -> To retrieve the preceding commit.
#          submodules: true # OR "recursive" -> To include all changed submodule files.

      - name: Get changed files using defaults
        id: changed-files
        uses: tj-actions/changed-files@v23

      - name: Get changed files using a comma separator
        id: changed-files-comma
        uses: tj-actions/changed-files@v23
        with:
          separator: ","

      - name: List all added files
        run: |
          for file in ${{ steps.changed-files.outputs.added_files }}; do
            echo "$file was added"
          done

      - name: Run step when a file changes
        if: contains(steps.changed-files.outputs.modified_files, 'my-file.txt')
        run: |
          echo "Your my-file.txt file has been modified."

      - name: Run step when a file has been deleted
        if: contains(steps.changed-files.outputs.deleted_files, 'test.txt')
        run: |
          echo "Your test.txt file has been deleted."

      - name: Get specific changed files
        id: changed-files-specific
        uses: tj-actions/changed-files@v23
        with:
          files: |
            my-file.txt
            test.txt
            new.txt
            test_directory
            *.sh
            *.png
            !*.md
            *.jpeg
            **/migrate-*.sql
          files_ignore: |
            *.yml

      - name: Run step if any of the listed files above change
        if: steps.changed-files-specific.outputs.any_changed == 'true'
        run: |
          echo "One or more files listed above has changed."

      - name: Run step if only the files listed above change
        if: steps.changed-files-specific.outputs.only_changed == 'true'
        run: |
          echo "Only files listed above have changed."

      - name: Run step if any of the listed files above is deleted
        if: steps.changed-files.outputs.any_deleted == 'true'
        run: |
          for file in ${{ steps.changed-files.outputs.deleted_files }}; do
            echo "$file was deleted"
          done

      - name: Run step if all listed files above have been deleted
        if: steps.changed-files.outputs.only_deleted == 'true'
        run: |
          for file in ${{ steps.changed-files.outputs.deleted_files }}; do
            echo "$file was deleted"
          done

      - name: Use a source file or list of file(s) to populate to files input.
        id: changed-files-specific-source-file
        uses: tj-actions/changed-files@v23
        with:
          files_from_source_file: |
            test/changed-files-list.txt

      - name: Use a source file or list of file(s) to populate to files input and optionally specify more files.
        id: changed-files-specific-source-file-and-specify-files
        uses: tj-actions/changed-files@v23
        with:
          files_from_source_file: |
            test/changed-files-list.txt
          files: |
            test.txt

      - name: Use a different commit SHA
        id: changed-files-custom-sha
        uses: tj-actions/changed-files@v23
        with:
          sha: ${{ github.event.pull_request.head.sha }}

      - name: Use a different base SHA
        id: changed-files-custom-base-sha
        uses: tj-actions/changed-files@v23
        with:
          base_sha: "2096ed0"
          
      - name: Checkout into dir1
        uses: actions/checkout@v2
        with:
          fetch-depth: 0
          path: dir1

      - name: Run changed-files with defaults on the dir1
        id: changed-files-for-dir1
        uses: tj-actions/changed-files@v23
        with:
          path: dir1

      - name: List all added files in dir1
        run: |
          for file in ${{ steps.changed-files-for-dir1.outputs.added_files }}; do
            echo "$file was added"
          done

      - name: Run changed-files using the last commit on the remote branch
        id: changed-files-since-last-remote-commit
        uses: tj-actions/changed-files@v23
        with:
          since_last_remote_commit: "true"
      
      - name: Run changed-files using the fork point of a pull request
        id: changed-files-fork-point
        uses: tj-actions/changed-files@v23
        with:
          use_fork_point: "true"
          
      - name: Run changed-files with quotepath disabled
        id: changed-files-quotepath
        uses: tj-actions/changed-files@v23
        with:
          quotepath: "false"
      
      # Run changed-files action using the last successful commit as the base_sha
      # NOTE: This setting overrides the commit sha used by setting since_last_remote_commit to true.
      # It is recommended to use either solution that works for your use case.
      
      # Push event based workflows
      - name: Get branch name
        id: branch-name
        uses: tj-actions/branch-names@v5

      - uses: nrwl/last-successful-commit-action@v1
        id: last_successful_commit_push
        with:
          branch: ${{ steps.branch-name.outputs.current_branch }} # Get the last successful commit for the current branch. 
          workflow_id: 'test.yml'
          github_token: ${{ secrets.GITHUB_TOKEN }}

      - name: Run changed-files with the commit of the last successful test workflow run
        id: changed-files-base-sha-push
        uses: tj-actions/changed-files@v23
        with:
          base_sha: ${{ steps.last_successful_commit_push.outputs.commit_hash }}

      # Pull request based workflows.
      - name: Get branch name
        id: branch-name
        uses: tj-actions/branch-names@v5
        if: github.event_name == 'pull_request'

      - uses: nrwl/last-successful-commit-action@v1
        id: last_successful_commit_pull_request
        if: github.event_name == 'pull_request'
        with:
          branch: ${{ steps.branch-name.outputs.base_ref_branch }} # Get the last successful commit on master or main branch 
          workflow_id: 'test.yml'
          github_token: ${{ secrets.GITHUB_TOKEN }}

      - name: Run changed-files with the commit of the last successful test workflow run on main
        if: github.event_name == 'pull_request'
        id: changed-files-base-sha-pull-request
        uses: tj-actions/changed-files@v23
        with:
          base_sha: ${{ steps.last_successful_commit_pull_request.outputs.commit_hash }}

      - name: Run changed-files with dir_names
        id: changed-files-dir-names
        uses: tj-actions/changed-files@v23
        with:
          dir_names: "true"
```

<img width="1147" alt="Screen Shot 2021-11-19 at 4 59 21 PM" src="https://user-images.githubusercontent.com/17484350/142696936-8b7ca955-7ef9-4d53-9bdf-3e0008e90c3f.png">

*   Free software: [MIT license](LICENSE)

## Known Limitation

> NOTE: :warning:
>
> *   Using characters like `\n`, `%`, `.` and `\r` as separators would be [URL encoded](https://www.w3schools.com/tags/ref_urlencode.asp)
> *   Spaces in file names can introduce bugs when using bash loops. See: [#216](https://github.com/tj-actions/changed-files/issues/216)
>     However, this action will handle spaces in file names, with a recommendation of using a separator to prevent hidden issues.
>     ![Screen Shot 2021-10-23 at 9 37 34 AM](https://user-images.githubusercontent.com/17484350/138558767-b13c90bf-a1ae-4e86-9520-70a6a4624f41.png)

## Versioning

This project follows a `v(major).(patch)` versioning scheme with the exception of pointing the git ref of the latest patch release to the major version tag.

> NOTE: :warning:
>
> *   Users referencing the legacy `v1.x.x` -> `v5.0.0` semantic versions, are required to switch over to `v10.x` -> `v15.x` respectively as new releases would no longer be deployed using the old versioning scheme.
> *   A breaking change was introduced in `v1.1.4` and `v13.x` which has been fixed.

## Migration guide

With the switch from using grep's Extended regex to match files to the natively supported workflow glob pattern matching syntax introduced in [v13](https://github.com/tj-actions/changed-files/releases/tag/v13) you'll need to modify patterns used to match `files`.

**BEFORE**

```yml
...

      - name: Get specific changed files
        id: changed-files-specific
        uses: tj-actions/changed-files@v12.2
        with:
          files: |
            \.sh$
            .(sql|py)$
            ^(mynewfile|custom)
```

**AFTER**

```yml
...

      - name: Get specific changed files
        id: changed-files-specific
        uses: tj-actions/changed-files@v23
        with:
          files: |
            *.sh
            *.sql
            *.py
            mynewfile
            custom/**
```

## Credits

This package was created with [Cookiecutter](https://github.com/cookiecutter/cookiecutter).

*   [tj-actions/glob](https://github.com/tj-actions/glob)
*   [tj-actions/demo](https://github.com/tj-actions/demo)

## Report Bugs

Report bugs at https://github.com/tj-actions/changed-files/issues.

If you are reporting a bug, please include:

*   Your operating system name and version.
*   Any details about your workflow that might be helpful in troubleshooting.
*   Detailed steps to reproduce the bug.

## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->

<!-- prettier-ignore-start -->

<!-- markdownlint-disable -->

<table>
  <tr>
    <td align="center"><a href="https://github.com/jsoref"><img src="https://avatars.githubusercontent.com/u/2119212?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Josh Soref</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=jsoref" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/monoxgas"><img src="https://avatars.githubusercontent.com/u/1223016?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Nick Landers</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=monoxgas" title="Code">💻</a></td>
    <td align="center"><a href="https://github.com/Kras4ooo"><img src="https://avatars.githubusercontent.com/u/1948054?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Krasimir Nikolov</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=Kras4ooo" title="Code">💻</a> <a href="https://github.com/tj-actions/changed-files/commits?author=Kras4ooo" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/IvanPizhenko"><img src="https://avatars.githubusercontent.com/u/11859904?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Ivan Pizhenko</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=IvanPizhenko" title="Code">💻</a> <a href="https://github.com/tj-actions/changed-files/commits?author=IvanPizhenko" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/talva-tr"><img src="https://avatars.githubusercontent.com/u/82046981?v=4?s=100" width="100px;" alt=""/><br /><sub><b>talva-tr</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=talva-tr" title="Code">💻</a></td>
    <td align="center"><a href="https://bandism.net/"><img src="https://avatars.githubusercontent.com/u/22633385?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Ikko Ashimine</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=eltociear" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/Zamiell"><img src="https://avatars.githubusercontent.com/u/5511220?v=4?s=100" width="100px;" alt=""/><br /><sub><b>James</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=Zamiell" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/wushujames"><img src="https://avatars.githubusercontent.com/u/677529?v=4?s=100" width="100px;" alt=""/><br /><sub><b>James Cheng</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=wushujames" title="Documentation">📖</a></td>
    <td align="center"><a href="https://qiita.com/SUZUKI_Masaya"><img src="https://avatars.githubusercontent.com/u/15100604?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Masaya Suzuki</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=massongit" title="Code">💻</a></td>
    <td align="center"><a href="https://fagai.net"><img src="https://avatars.githubusercontent.com/u/1772112?v=4?s=100" width="100px;" alt=""/><br /><sub><b>fagai</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=fagai" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/pkit"><img src="https://avatars.githubusercontent.com/u/805654?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Constantine Peresypkin</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=pkit" title="Code">💻</a></td>
    <td align="center"><a href="https://github.com/deronnax"><img src="https://avatars.githubusercontent.com/u/439279?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Mathieu Dupuy</b></sub></a><br /><a href="https://github.com/tj-actions/changed-files/commits?author=deronnax" title="Documentation">📖</a></td>
  </tr>
</table>

<!-- markdownlint-restore -->

<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!
