# Release Menu

| **English** | [日本語](/README/ja.md) |
| ----------- | ---------------------- |

Release Menu lets you know what is contained in the release PR by leaving a comment. It also has the ability to post the message to Slack.

| ![GitHub comment](/screenshots/github_comment.png) |
| :------------------------------------------------: |
| ![GitHub comment](/screenshots/slack_message.png)  |



## Features
* Easy-setup
* Fast



## Setup
All you have to do is put the following YAML code as `.github/workflows/release-menu.yml` on your project.

```yml
# .github/workflows/release-menu.yml

name: "Release Menu"

on:
  pull_request:
    types: [opened]

jobs:
  release-menu:
    if: startsWith(github.event.pull_request.head.ref, 'release')
    runs-on: ubuntu-latest
    steps:
      - name: "Menu"
        uses: noraworld/release-menu@v0.1.1
        with:
          # Your options here like this:
          # base: master
          # mode: optimized
```

### Options
All the options have the default values. So you need not specify them explicitly as long as their default values suit your project.

| Name                                            | Description                                                                                       | Default   | Remark                                                                                                                                                                                                  |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `jobs.release-menu.steps[*].with.head`          | A development branch name                                                                         | `develop` |                                                                                                                                                                                                         |
| `jobs.release-menu.steps[*].with.base`          | A production branch name                                                                          | `main`    | • You may need to set this to `master` if your project uses the "master" branch instead of the "main" branch                                                                                            |
| `jobs.release-menu.steps[*].with.checkbox`      | Whether a comment includes checkboxes                                                             | `true`    |                                                                                                                                                                                                         |
| `jobs.release-menu.steps[*].with.title`         | Whether a comment includes titles                                                                 | `false`   | • GitHub will detect the titles of the PRs automatically from PR numbers (see [the screenshot above](#release-menu))<br>• The titles will be included in a Slack message even if this is set to `false` |
| `jobs.release-menu.steps[*].with.author`        | Whether a comment includes author names                                                           | `true`    |                                                                                                                                                                                                         |
| `jobs.release-menu.steps[*].with.slack_token`   | A Slack bot token                                                                                 | `""`      | • It won’t post a message to Slack if you don’t specify this                                                                                                                                            |
| `jobs.release-menu.steps[*].with.slack_channel` | A Slack channel ID where you want to post a message                                               | `""`      | • It won’t post a message to Slack if you don’t specify this                                                                                                                                            |
| `jobs.release-menu.steps[*].with.mode`          | One of `merged`, `all`, or `optimized` (see [About mode](#about-mode) below for more information) | `merged`  |                                                                                                                                                                                                         |

### About mode
There are three types of mode and you can choose either one of them.

| Mode        | Description                                                                                                                                                                                                                              | Pros                                                                                                                                                                                                                                                    | Cons                                                                                                                                                                                                                                                             |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `merged`    | Detect PRs associated with only merge commits                                                                                                                                                                                            | • It invokes fewer GitHub APIs                                                                                                                                                                                                                          | • It can’t detect PRs that are merged with `squash and merge` or `rebase and merge`                                                                                                                                                                              |
| `all`       | Detect PRs associated with every commit that exists in the head branch and doesn’t exist in the base branch                                                                                                                              | • It can detect all PRs no matter which way you merge a pull request in                                                                                                                                                                                 | • GitHub API rate limit might be exceeded because it invokes as many GitHub APIs as commits                                                                                                                                                                      |
| `optimized` | Detect PRs that are a combination of PRs associated with merge commits and those associated with commits that remain after subtracting all commits included in PRs associated with merge commits from commits that are not merge commits | • It can detect all PRs no matter which way you merge a pull request in<br>• It invokes fewer GitHub APIs than the mode `all` if `rebase and merge` of a PR merge option is not used on your project and there is no commits not associated with any PR | • It invokes more GitHub APIs than the mode `all` if `rebase and merge` of a PR merge option is used on your project and there are a lot of commits not associated with any PRs<br>• It might potentially contain bugs because its implementation is complicated |

#### So, which one should I choose?
If you and your members always create a merge commit when you merge a pull request on your project, choose `merged`. There is no need to specify it because `merged` is the default value.

If you and your members can squash commits, **and** if you **don’t** rebase commits or push commits directly into the head branch without a PR on your project, `optimized` will be the best option.

If you and your members can use all these methods on your project, `all` is likely to be more acceptable.



## Similar projects
* [git-pr-release](https://github.com/x-motemen/git-pr-release)
* [github-pr-release](https://github.com/uiur/github-pr-release)



## License
All codes of this project are available under the MIT license. See the [LICENSE](/LICENSE) for more information.
