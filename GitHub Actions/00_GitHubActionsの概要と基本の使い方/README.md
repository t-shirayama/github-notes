# GitHub Actions の概要と基本の使い方

GitHub Actions は、GitHub 上のイベントをきっかけに処理を自動実行する仕組み。テスト、Lint、ビルド、デプロイ、IssueやPRの自動操作など、リポジトリに関係する作業をワークフローとして定義できる。

## できること

| 用途 | 例 |
| --- | --- |
| CI | pushやPR時にテスト、Lint、型チェックを実行する |
| CD | mainブランチにマージされたら自動デプロイする |
| セキュリティ | CodeQL、Gitleaks、依存関係チェックを実行する |
| リリース | タグ作成、リリースノート生成、パッケージ公開を自動化する |
| 運用補助 | Issueのラベル付け、PRコメント、定期実行タスクを行う |

## 基本用語

| 用語 | 意味 |
| --- | --- |
| workflow | 自動化したい処理全体。`.github/workflows/*.yml` または `.github/workflows/*.yaml` に書く |
| event | workflowを起動するきっかけ。`push`、`pull_request`、`schedule` など |
| job | workflow内のまとまった処理単位。通常は1つのrunner上で実行される |
| step | job内の1つひとつの処理。コマンド実行やaction呼び出しを書く |
| action | 再利用できる処理部品。`actions/checkout` や `actions/setup-node` など |
| runner | jobを実行するマシン。GitHub-hosted runnerとself-hosted runnerがある |
| artifact | workflowの成果物。テストレポート、ビルド結果、ログなどを保存できる |

## ファイルの置き場所

ワークフローはリポジトリ内の `.github/workflows/` 配下に置く。

```text
.github/
└── workflows/
    ├── ci.yml
    └── deploy.yml
```

ファイル名は自由だが、拡張子は `.yml` または `.yaml` にする。用途が分かる名前にすると管理しやすい。

- `ci.yml`: テスト、Lint、型チェック
- `deploy.yml`: デプロイ
- `codeql.yml`: CodeQL解析
- `release.yml`: リリース作業

## 最小ワークフロー

PRとmainブランチへのpushでテストを実行する例。

```yaml
name: CI

on:
  pull_request:
  push:
    branches:
      - main

permissions:
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v6

      - name: Setup Node.js
        uses: actions/setup-node@v6
        with:
          node-version: 24
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test
```

## ワークフローの読み方

```yaml
name: CI
```

GitHubのActions画面に表示される名前。

```yaml
on:
  pull_request:
```

どのイベントで実行するかを指定する。`pull_request` ならPR作成や更新時に実行される。

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
```

`test` というjobを、UbuntuのGitHub-hosted runnerで実行する。

```yaml
steps:
  - name: Install dependencies
    run: npm ci
```

stepでは、シェルコマンドを実行する `run` と、再利用部品を呼び出す `uses` をよく使う。

## よく使うイベント

| イベント | 使いどころ |
| --- | --- |
| `push` | mainブランチへのpushやタグ作成をきっかけにする |
| `pull_request` | PRの作成、更新、再オープン時に実行する |
| `workflow_dispatch` | GitHub画面から手動実行できるようにする |
| `schedule` | cron形式で定期実行する |
| `release` | GitHub Releaseの作成時に実行する |

手動実行を許可する例。

```yaml
on:
  workflow_dispatch:
```

毎日午前3時に実行する例。GitHub ActionsのcronはUTC基準。

```yaml
on:
  schedule:
    - cron: "0 18 * * *" # 日本時間の午前3時
```

`schedule` はdefault branch上のワークフローだけで実行される。また、GitHub Actionsの混雑状況によって遅延したり、負荷が高い場合は実行されないことがある。毎時0分など混みやすい時刻は避けるとよい。

## jobを分ける

処理の責務が違う場合はjobを分ける。

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: actions/setup-node@v6
        with:
          node-version: 24
          cache: npm
      - run: npm ci
      - run: npm run lint

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: actions/setup-node@v6
        with:
          node-version: 24
          cache: npm
      - run: npm ci
      - run: npm test
```

jobは基本的に並列実行される。順番を指定したい場合は `needs` を使う。

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - run: npm test

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - run: npm run deploy
```

## matrixで複数環境を試す

複数のNode.jsバージョンやOSで同じテストを実行したい場合は `strategy.matrix` を使う。

```yaml
jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [22, 24]

    steps:
      - uses: actions/checkout@v6
      - uses: actions/setup-node@v6
        with:
          node-version: ${{ matrix.node-version }}
          cache: npm
      - run: npm ci
      - run: npm test
```

## 環境変数とシークレット

通常の設定値は `env`、秘密情報はGitHubのSecretsに登録して使う。

```yaml
env:
  NODE_ENV: test

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - run: echo "$NODE_ENV"
```

シークレットを使う例。

```yaml
steps:
  - name: Deploy
    env:
      API_TOKEN: ${{ secrets.API_TOKEN }}
    run: npm run deploy
```

シークレットはログに出さない。`echo` したり、エラー出力に混ざるコマンドへ渡したりしない。

## cacheとartifact

`cache` は依存関係の再利用に使う。`actions/setup-node` の `cache: npm` のように、公式actionの機能で設定できることも多い。

`artifact` はworkflowの成果物を保存するために使う。

```yaml
- name: Upload coverage
  uses: actions/upload-artifact@v7
  with:
    name: coverage
    path: coverage/
```

使い分けは次の通り。

| 仕組み | 目的 | 例 |
| --- | --- | --- |
| cache | 次回以降の実行を速くする | npm、pnpm、Gradleの依存関係 |
| artifact | 実行結果を後から確認する | カバレッジ、E2Eのスクリーンショット、ビルド成果物 |

## 実行結果の見方

1. GitHubのリポジトリで `Actions` タブを開く。
2. 左側からworkflowを選ぶ。
3. 実行履歴から失敗したrunを開く。
4. 失敗したjobとstepを確認する。
5. ログの最後だけでなく、最初のエラーも見る。

失敗時は、次の順で見ると切り分けやすい。

- 依存関係のインストールで失敗していないか。
- 環境変数やSecretsが足りているか。
- ローカルとCIでNode.jsやRubyなどのバージョンが違わないか。
- テストが時刻、並列実行、外部APIに依存していないか。
- パス指定がWindows前提やmacOS前提になっていないか。

## 最初に作るなら

Webアプリでは、最初は1つの `ci.yml` にまとめると分かりやすい。

```text
.github/workflows/ci.yml
```

中身は次の順で増やす。

1. `npm ci`
2. `npm test`
3. `npm run lint`
4. `npm run typecheck`
5. カバレッジやE2Eテスト

デプロイやセキュリティスキャンが増えてきたら、`deploy.yml`、`codeql.yml` のように用途ごとに分ける。

## 安全に使うための基本

- `permissions: contents: read` をまず設定し、必要な権限だけ追加する。
- SecretsはPRログに出さない。
- フォークPRでSecretsを使う設計にしない。通常、フォークPRには `GITHUB_TOKEN` 以外のSecretsは渡されず、`GITHUB_TOKEN` も読み取り権限に制限される。
- `pull_request_target` は権限が強くなりやすいので、必要な場合だけ使う。
- `pull_request_target` でPR側のコードをcheckoutして実行しない。
- サードパーティActionは、信頼できるものを選び、可能ならcommit SHAで固定する。
- デプロイ用の長期トークンより、OIDC認証を優先する。

## よくあるつまずき

| 症状 | よくある原因 |
| --- | --- |
| workflowが実行されない | ファイルが `.github/workflows/` にない、YAML構文エラー、対象ブランチが違う |
| `npm ci` が失敗する | lockファイルがない、`package.json` とlockファイルがずれている |
| ローカルでは通るがCIで落ちる | OS、Node.jsバージョン、環境変数、タイムゾーンの違い |
| Secretsが空になる | リポジトリやEnvironmentに登録していない、フォークPRから実行している |
| deployが重複する | `concurrency` を設定していない |

## 関連記事

- [Webアプリ開発 GitHub Actions おすすめ設定](../01_Webアプリ開発おすすめ設定/README.md)

## 参考リンク

- [GitHub Docs: GitHub Actions documentation](https://docs.github.com/en/actions/)
- [GitHub Docs: Workflows](https://docs.github.com/actions/concepts/workflows-and-actions/workflows)
- [GitHub Docs: Workflow syntax for GitHub Actions](https://docs.github.com/actions/reference/workflows-and-actions/workflow-syntax)
- [GitHub Docs: Contexts reference](https://docs.github.com/en/actions/learn-github-actions/contexts)
- [GitHub Docs: Security hardening for GitHub Actions](https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions)
