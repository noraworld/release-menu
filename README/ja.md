# Release Menu

| [English](/README/en.md) | **日本語** |
| ------------------------ | --------- |

Release Menu は、何がリリースされるのかをリリースプルリクエスト (以下 PR) に自動的にコメントする GitHub Actions です。Slack に投稿する機能もあります。

| ![GitHub comment](/screenshots/github_comment.png) |
| :------------------------------------------------: |
| ![GitHub comment](/screenshots/slack_message.png)  |



## 特長
* 簡単セットアップ
* 高速に動作



## セットアップ
`.github/workflows/release-menu.yml` に以下のような YAML ファイルを置くだけで動作します。

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
        uses: noraworld/release-menu@v0.1.0
        with:
          # Your options here like this:
          # base: master
          # mode: optimized
```

### オプション
以下はオプションです。すべてのオプションにはデフォルト値があるため、デフォルト値と同じであれば明示的に値を指定する必要はありません。

| オプション名 | 概要 | デフォルト値 | 備考 |
| --- | --- | --- | --- |
| `jobs.release-menu.steps[*].with.head`          | 開発ブランチ名 | `develop` | |
| `jobs.release-menu.steps[*].with.base`          | 本番ブランチ名 | `main` | • `main` ブランチではなく `master` ブランチを使用している場合は書き換える必要があるかもしれません |
| `jobs.release-menu.steps[*].with.checkbox`      | コメントにチェックボックスを含めるかどうか | `true` | |
| `jobs.release-menu.steps[*].with.title`         | コメントに PR のタイトルを含めるかどうか | `false` | • GitHub は PR の番号からタイトルを自動的に検出します ([上記スクリーンショット](#release-menu) 参照)<br>• `false` に設定しても Slack にはタイトルが含まれます |
| `jobs.release-menu.steps[*].with.author`        | コメントに PR の投稿者を含めるかどうか | `true` | |
| `jobs.release-menu.steps[*].with.slack_token`   | Slack ボットトークン | `""` | • この値を指定しなかった場合は Slack に投稿されません |
| `jobs.release-menu.steps[*].with.slack_channel` | メッセージを投稿する Slack チャンネル名 | `""` | • この値を指定しなかった場合は Slack に投稿されません |
| `jobs.release-menu.steps[*].with.mode`          | `merged`, `all`, `optimized` の中から選択 (詳細は下記の [モードについて](#モードについて) を参照) | `merged` | |

### モードについて
3 種類のモードがありいずれか 1 つを選択します。それぞれのモードについての特長は以下のとおりです。

| モード名 | 概要 | メリット | デメリット |
| --- | --- | --- | --- |
| `merged`    | マージコミットに紐付いた PR のみ検出します | • 呼び出す GitHub API の回数を少なくできます | • `squash and merge` または `rebase and merge` でマージされた PR は検出できません |
| `all`       | ヘッドブランチに存在しベースブランチに存在しないすべてのコミットに紐付いた PR を検出します | • どのような方法で PR をマージしても検出することができます | • コミットの数だけ GitHub API を呼び出すため利用制限がかかるかもしれません |
| `optimized` | マージコミットに紐づく PR と、マージコミットではないコミットの中からマージコミットに紐づく PR の中に含まれるすべてのコミットを引いて残ったコミットに紐づく PR を組み合わせた PR を検出します | • どのような方法で PR をマージしても検出することができます<br>• PR マージ時に `rebase and merge` を使わず、かつ PR に紐付かないコミットがない場合は、モード `all` よりも呼び出す GitHub API の回数を少なくできます | • PR マージ時に `rebase and merge` を使っていたり PR に紐付かないコミットがたくさんある場合は、モード `all` よりも呼び出す GitHub API の回数が多くなる場合があります<br>• 実装が複雑なため潜在的なバグが含まれている可能性があります |

#### 結局どれが最適ですか？
PR マージ時に常にマージコミットを作成する場合は `merged` を選択してください。なお、`merged` がデフォルト値であるためこの場合は明示的に指定する必要はありません。

PR マージ時にコミットをスカッシュすることはあっても、リベースしたりヘッドブランチに直接コミットをプッシュしたりしないのであれば `optimized` が最適だと考えられます。

上記のどれも使用する場合は `all` が無難な選択肢です。



## 関連プロジェクト
* [git-pr-release](https://github.com/x-motemen/git-pr-release)
* [github-pr-release](https://github.com/uiur/github-pr-release)



## ライセンス
本プロジェクト内のすべてのコードは MIT ライセンスに基づき使用することができます。詳細については [LICENSE](/LICENSE) をご覧ください。
