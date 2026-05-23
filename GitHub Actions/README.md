# GitHub Actions

GitHub Actions の設定、CI/CD、セキュリティ、運用まわりのメモ。

## 一覧

- [GitHub Actions の概要と基本の使い方](00_GitHubActionsの概要と基本の使い方/README.md)
- [Webアプリ開発 GitHub Actions おすすめ設定](01_Webアプリ開発おすすめ設定/README.md)

## 使いどころ

- Webアプリのリポジトリを作った直後に、最低限のCIを整える。
- PR時にテスト、Lint、型チェック、セキュリティ確認を自動化する。
- デプロイやリリース作業を手動運用から少しずつ自動化する。
- GitHub Actions自体の権限やサプライチェーンリスクを見直す。

## おすすめの読み順

1. GitHub Actions の基本を知りたい場合は [GitHub Actions の概要と基本の使い方](00_GitHubActionsの概要と基本の使い方/README.md) を読む。
2. Web アプリに最低限の CI を入れたい場合は [Webアプリ開発 GitHub Actions おすすめ設定](01_Webアプリ開発おすすめ設定/README.md) の「まず入れる最小セット」まで読む。
3. 運用前に `permissions`、Dependabot、Actions SHA 固定、OIDC などのセキュリティ設定を確認する。

## 注意点

- 最初から全部入れるより、必須のCIから小さく始める。
- ワークフローの失敗原因が分かるように、ジョブ名とステップ名を具体的にする。
- セキュリティ系の設定は、検知した後の対応フローも一緒に決める。
