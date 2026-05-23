# GitHub Actions トラブルシュート

GitHub Actions が失敗したときは、先に失敗したjobとstepを絞り込みます。ログ全体を読むより、落ちた場所と直前の変更差分を見る方が早く原因に近づけます。

## まず見る場所

- 失敗したjob。
- 失敗したstep。
- エラーメッセージ。
- 直前の変更差分。
- ローカルとCIの実行コマンドの違い。

## よくある原因

| 原因 | 見るところ |
| ---- | ---------- |
| Node.jsやJavaのバージョン違い | `setup-node`、`setup-java`、`.node-version`、`package.json` |
| 依存関係のlock file差分 | `package-lock.json`、`pnpm-lock.yaml`、`Gemfile.lock` |
| 環境変数やSecrets不足 | Repository secrets、Environment secrets |
| 権限不足 | `permissions`、GITHUB_TOKENの権限 |
| キャッシュ不整合 | cache key、lock file、依存関係の更新履歴 |
| テストの不安定さ | 失敗箇所、タイムアウト、並列実行、外部API |

## 対応方針

1. 失敗したstepのログを読む。
2. ローカルで同じコマンドを実行する。
3. 依存関係を入れ直して再現する。
4. Secretsやpermissionsを確認する。
5. キャッシュを無効化して確認する。
6. 失敗が不安定なら、再実行して発生条件を絞る。

## ローカルで確認する

Node.js系なら、CIと同じ順番で確認します。

```sh
npm ci
npm test
npm run build
```

lock fileがあるプロジェクトでは、CIでもローカルでも `npm install` ではなく `npm ci` を使うと差分に気づきやすくなります。

## permissions を確認する

ワークフローでIssue、PR、packages、deploymentsなどを操作する場合は、必要な権限を明示します。

```yaml
permissions:
  contents: read
  pull-requests: write
```

権限不足のエラーが出た場合でも、広く `write-all` にする前に、必要な権限だけを追加します。

## キャッシュを疑う

依存関係を更新した直後にだけ落ちる場合は、キャッシュが古い可能性があります。

- cache keyにlock fileのhashが含まれているか確認する。
- 一度キャッシュを無効化して実行する。
- setup系actionのcache設定を確認する。

## 再実行する前に

再実行だけで直る失敗は、テストの不安定さや外部サービス依存が原因かもしれません。何度も再実行して通すより、失敗条件をメモしてから原因を分けます。
