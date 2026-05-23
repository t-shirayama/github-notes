# Webアプリ開発 GitHub Actions おすすめ設定

Webアプリ開発でよく使う GitHub Actions の設定を、品質、セキュリティ、サプライチェーン、デプロイ、可視化の観点で整理する。最初から全部入れる必要はなく、プロジェクトの規模とリスクに合わせて段階的に導入する。

## 優先度の凡例

| 優先度 | 意味 |
| --- | --- |
| 🔴 必須 | 入れない理由が少ない。早い段階で入れる |
| 🟡 推奨 | 規模、要件、運用コストに応じて入れる |
| 🟢 余裕があれば | 品質や信頼性をさらに上げたいときに入れる |

## まず入れる最小セット

Webアプリなら、まずは次の4つをPR時に自動実行する。

| 項目 | 例 | 目的 |
| --- | --- | --- |
| テスト | Jest、Vitest、RSpec | 既存機能の破壊を検知する |
| Lint | ESLint、RuboCop | バグになりやすい書き方を検知する |
| 型チェック | `tsc --noEmit` | TypeScriptの型エラーを検知する |
| 依存関係チェック | Dependabot | 脆弱性や古い依存関係を検知する |

## 1. コード品質

| ツール | 役割 | 優先度 | 補足 |
| --- | --- | --- | --- |
| CI | テスト自動実行 | 🔴 必須 | Jest、Vitest、RSpecなどをPRごとに実行する |
| ESLint / RuboCop | Lint | 🔴 必須 | コードの危険な書き方や規約違反を検知する |
| Prettier / StandardRB | フォーマット統一 | 🟡 推奨 | 差分を読みやすくし、レビューの指摘を減らす |
| TypeScript | 型チェック | 🟡 推奨 | `tsc --noEmit` で型だけ検証する |
| Codecov / Coveralls | カバレッジ可視化 | 🟡 推奨 | READMEバッジやPRコメントでテスト状況を見える化する |
| Playwright / Cypress | E2Eテスト | 🟢 余裕があれば | 重要なユーザー操作だけに絞ると運用しやすい |

### 補足

- PRで必ず通したいジョブは、GitHubのブランチ保護ルールで required checks にする。
- カバレッジ率だけを目標にせず、認証、決済、権限、データ更新など重要な分岐を優先する。
- E2Eテストは壊れやすいので、まずはログイン、主要CRUD、デプロイ後の疎通確認などに絞る。

## 2. セキュリティ

| ツール | 役割 | 優先度 | 補足 |
| --- | --- | --- | --- |
| Dependabot alerts | 依存パッケージのCVE検知 | 🔴 必須 | GitHub標準機能として有効化しやすい |
| CodeQL | ソースコード静的解析、SAST | 🟡 推奨 | JavaScript、TypeScript、Ruby、Javaなどで使える |
| OWASP ZAP | 動的スキャン、DAST | 🟡 推奨 | ステージング環境に対して実行する |
| OSV-Scanner | 脆弱性DBとの照合 | 🟡 推奨 | Google系のOSVデータベースを利用する |
| Gitleaks | シークレット漏洩検知 | 🟡 推奨 | `.env`、APIキー、トークンのコミット混入を検知する |
| GitGuardian | シークレット漏洩検知 | 🟢 余裕があれば | Gitleaksより高機能な商用寄りの選択肢 |

### 補足

- Gitleaksは個人開発でも入れる価値が高い。`.env`、クラウドキー、GitHubトークンは一度pushすると取り消しやローテーションが必要になる。
- CodeQLは「アプリのコード」を見る。Gitleaksは「秘密情報の混入」を見る。役割が違うので併用できる。
- DASTは本番ではなく、検証環境やステージング環境を対象にする。

## 3. サプライチェーン

| ツール / 設定 | 役割 | 優先度 | 補足 |
| --- | --- | --- | --- |
| Dependabot version updates | パッケージ更新PRの自動作成 | 🔴 必須 | 依存関係を古いまま放置しにくくする |
| OSV-Scanner | 既知の脆弱性や侵害パッケージの照合 | 🟡 推奨 | lockファイルを対象にすると実際の導入バージョンを確認しやすい |
| OpenSSF Scorecard | リポジトリ全体のセキュリティ健全性スコア | 🟡 推奨 | Branch Protection、Token Permissions、Pinned Dependenciesなど複数の観点を確認できる |
| Actions SHA固定 | サードパーティActionをcommit SHAで固定 | 🟡 推奨 | タグ差し替え攻撃の影響を受けにくくする |
| Socket.dev | denylistチェック、install script検知 | 🟢 余裕があれば | npm系の依存関係リスクを深く見たいときに使う |
| lockfile-lint | 不正レジストリやlockファイル改ざん検知 | 🟢 余裕があれば | npmのlockファイル確認に使いやすい |

### Actions SHA固定の考え方

GitHub Actionsの `uses:` はタグ指定よりcommit SHA指定の方が安全性が高い。

```yaml
# タグ指定
- uses: actions/checkout@v6

# SHA固定
- uses: actions/checkout@<40文字のcommit SHA> # v6.x.x の例
```

タグは後から差し替えられる可能性がある。commit SHAで固定すると、意図しないコードに差し替わるリスクを下げられる。一方で、更新作業は少し重くなるので、DependabotやRenovateでActions更新PRを作る運用と合わせるとよい。実際に固定するときは、利用するタグが指すcommit SHAを確認してから置き換える。

2026年3月には、Trivy関連のGitHub Actionsタグが攻撃者のcommitに差し替えられた事例があった。このような背景から、サードパーティActionのSHA固定はより重要になっている。

### OpenSSF Scorecardの使いどころ

OpenSSF Scorecardは、リポジトリのセキュリティ状態を複数の観点からスコアリングする。ブランチ保護、依存関係の固定、トークン権限、脆弱性管理などをまとめて確認でき、結果をREADMEバッジとして貼ることもできる。

## 4. パフォーマンス

| ツール | 役割 | 優先度 | 補足 |
| --- | --- | --- | --- |
| Lighthouse CI | パフォーマンス、アクセシビリティ、SEO計測 | 🟡 推奨 | デプロイ後やPRプレビューに対して実行する |
| Bundle Analyzer / bundlesize | JSバンドルサイズ監視 | 🟡 推奨 | フロントエンドの肥大化を早めに検知する |

### 補足

- Lighthouse CIはスコアの揺れがあるため、最初はレポート確認から始める。
- バンドルサイズは「増加量」を見るとレビューしやすい。

## 5. リリース・デプロイ

| ツール / 設定 | 役割 | 優先度 | 補足 |
| --- | --- | --- | --- |
| CD | 自動デプロイ | 🔴 必須 | Vercel、Render、Fly.io、AWS、GCPなどに自動反映する |
| Release Drafter | リリースノート自動生成 | 🟡 推奨 | PRラベルをもとにリリース内容を整理できる |
| Semantic Release | バージョン自動管理 | 🟢 余裕があれば | Conventional Commitsと相性がよい |
| Docker Build & Push | コンテナイメージ自動ビルド・プッシュ | 🟢 余裕があれば | GHCRやクラウドのレジストリにpushする |
| Terraform Plan / Apply | IaCの差分確認と適用 | 🟢 余裕があれば | `plan` はPR、`apply` はmainマージ後などに分ける |

### 補足

- デプロイ用の認証情報は長期トークンよりOIDC認証を優先する。
- 本番デプロイは GitHub Environments の承認ルールを使うと事故を減らせる。
- `concurrency` を設定すると、古いデプロイジョブの競合を避けやすい。

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

## 6. 通知・可視化

| ツール / 設定 | 役割 | 優先度 | 補足 |
| --- | --- | --- | --- |
| READMEバッジ | CI、Coverage、Scorecardなどの状態可視化 | 🟡 推奨 | リポジトリの健康状態が一目で分かる |
| Slack通知 | デプロイ成功、失敗を通知 | 🟢 余裕があれば | 失敗時だけ通知するとノイズを減らせる |
| TypeDoc / JSDoc | APIドキュメント自動生成 | 🟢 余裕があれば | ライブラリや共通モジュールが多い場合に有効 |

### 補足

- バッジは増やしすぎると読みづらい。CI、Coverage、Scorecardくらいから始める。
- 通知は「誰が見るか」「失敗時に何をするか」まで決める。

## 7. ワークフロー設定のベストプラクティス

ツールを増やす前に、GitHub Actions自体の設定を安全にする。

| 設定 | 役割 | 優先度 | 補足 |
| --- | --- | --- | --- |
| `permissions` の最小化 | `GITHUB_TOKEN` の権限を必要最小限にする | 🟡 推奨 | まずは `contents: read` から始める |
| OIDC認証 | 長期トークンを使わず短命トークンでクラウド認証する | 🟡 推奨 | AWS、GCP、Azureなどで利用できる |
| Actions SHA固定 | サードパーティActionをcommit SHAで固定する | 🟡 推奨 | タグ差し替え攻撃のリスクを下げる |
| `pull_request_target` の不使用 | フォークPRからのシークレット漏洩リスクを避ける | 🟡 推奨 | 必要な場合もcheckoutや権限に強い注意が必要 |
| `concurrency` | 古いジョブやデプロイの競合を避ける | 🟡 推奨 | デプロイ系で特に有効 |
| Environments | 本番デプロイの承認、保護ルールを設定する | 🟡 推奨 | productionだけ手動承認にするなど |

### 最小権限の例

```yaml
permissions:
  contents: read
```

PRにコメントを書く、パッケージをpushする、OIDCを使うなどの場合は必要な権限だけを追加する。

```yaml
permissions:
  contents: read
  pull-requests: write
  id-token: write
```

## 最小CIの例

Node.js / TypeScript系Webアプリの最小例。
`actions/checkout` や `actions/setup-node` の最新 major は変わるため、実際に導入するときは各 Action の公式 README や Marketplace で現在の推奨バージョンを確認する。

```yaml
name: CI

on:
  pull_request:
  push:
    branches:
      - main

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

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

      - name: Lint
        run: npm run lint

      - name: Type check
        run: npm run typecheck

      - name: Test
        run: npm test
```

実運用では `uses:` をcommit SHAで固定する。この記事では読みやすさを優先してタグ指定の形で示している。Actionの最新majorは変わるため、導入時は公式READMEやMarketplaceで現在の推奨バージョンを確認する。

## ロードマップ

| Step | 目的 | 入れるもの |
| --- | --- | --- |
| Step 1 | 基盤 | CI、Lint、型チェック、Dependabot |
| Step 2 | 品質可視化 | カバレッジ、READMEバッジ、CD |
| Step 3 | セキュリティ | CodeQL、Gitleaks、OSV-Scanner、OWASP ZAP |
| Step 4 | サプライチェーン・信頼性 | OpenSSF Scorecard、Actions SHA固定、`permissions` 最小化 |
| Step 5 | プロ感 | Lighthouse CI、Semantic Release、Docker、OIDC認証 |
| Step 6 | 上級 | E2Eテスト、Bundle分析、Terraform、Socket.dev |

## 導入時のチェックリスト

- [ ] PRでテスト、Lint、型チェックが走る
- [ ] mainブランチにrequired checksを設定している
- [ ] Dependabot alertsとversion updatesを有効化している
- [ ] `permissions` を必要最小限にしている
- [ ] シークレット検知を導入している
- [ ] デプロイ用の長期トークンを避け、OIDCを検討している
- [ ] サードパーティActionのSHA固定方針を決めている
- [ ] 失敗時の通知先と対応者が決まっている

## まとめ

最初に整えるべきなのは、CI、Lint、型チェック、Dependabotの4つ。次に、カバレッジやCDで品質とリリースを見える化する。セキュリティ面では、CodeQL、Gitleaks、OSV-Scannerを足し、サプライチェーン対策としてOpenSSF Scorecard、Actions SHA固定、`permissions` 最小化を進める。

特にGitleaks、OpenSSF Scorecard、Actions SHA固定、ワークフロー権限の最小化は、GitHub Actionsを安全に運用するうえで重要度が上がっている。ツールを増やすだけでなく、検知した後に誰がどう対応するかまで決めておく。

## 関連記事

- [GitHub Actions の概要と基本の使い方](../00_GitHubActionsの概要と基本の使い方/README.md)

## 参考リンク

- [GitHub Docs: Security hardening for GitHub Actions](https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions)
- [GitHub Docs: Assigning permissions to jobs](https://docs.github.com/en/actions/using-jobs/assigning-permissions-to-jobs)
- [GitHub Docs: OpenID Connect](https://docs.github.com/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)
- [GitHub Docs: Dependabot](https://docs.github.com/en/code-security/dependabot)
- [GitHub Docs: CodeQL](https://docs.github.com/en/code-security/code-scanning/introduction-to-code-scanning/about-code-scanning-with-codeql)
- [OpenSSF Scorecard](https://github.com/ossf/scorecard)
- [Gitleaks](https://github.com/gitleaks/gitleaks)
- [OSV-Scanner](https://github.com/google/osv-scanner)
- [OWASP ZAP](https://www.zaproxy.org/)
- [Aqua Security: Trivy ecosystem supply chain temporarily compromised](https://github.com/aquasecurity/trivy/security/advisories/GHSA-69fq-xp46-6x23)
