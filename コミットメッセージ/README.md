# コミットメッセージ規約

このページは Conventional Commits をベースに、Angular のコミット規約を参考にした運用メモです。チームによって body 必須・scope 候補・文字数制限は変わります。

## 基本フォーマット

コミットメッセージは **ヘッダー・ボディ・フッター** の3パートで構成されます。

```sh
<type>(<scope>): <short summary>

<body>

<footer>
```

- **ヘッダー**: 必須
- **ボディ**: `docs` type 以外では必須（20文字以上）
- **フッター**: 任意
- 各行は **100文字以内**

---

## ヘッダー

```sh
<type>(<scope>): <short summary>
│       │               └─ 現在形・小文字始まり・末尾にピリオドなし
│       └─ 影響を受けるパッケージ名（任意）
└─ コミットの種別（必須）
```

### type（種別）

| type       | 説明                                   |
| ---------- | -------------------------------------- |
| `feat`     | 新機能の追加                           |
| `fix`      | バグ修正                               |
| `perf`     | パフォーマンス改善                     |
| `refactor` | バグ修正・機能追加を伴わないコード変更 |
| `docs`     | ドキュメントのみの変更                 |
| `test`     | テストの追加・修正                     |
| `build`    | ビルドシステムや外部依存関係への変更   |
| `ci`       | CIの設定ファイル・スクリプトへの変更   |

### scope（スコープ）

scope はチームやプロジェクトに合わせて決めます。

Angular 風に運用する場合の例:

```sh
animations | bazel | benchpress | common | compiler | compiler-cli | core |
elements | forms | http | language-service | localize | platform-browser |
platform-browser-dynamic | platform-server | router | service-worker |
upgrade | zone.js | packaging | changelog | docs-infra | migrations | devtools
```

特殊なスコープ:

- `packaging` — 全パッケージのnpmレイアウト変更
- `changelog` — CHANGELOG.mdの更新
- `docs-infra` — `/adev` 配下のドキュメントアプリのインフラ変更

### summary（サマリー）

- **現在形・命令形**で記述（`"change"` であり `"changed"` や `"changes"` ではない）
- 先頭を**大文字にしない**
- 末尾に**ピリオドをつけない**

---

## ボディ

- サマリーと同様に**現在形・命令形**で記述
- 変更の**動機**と**以前の動作との比較**を含める
- サマリーの後に**1行空行**を挟む

---

## フッター

### Breaking Change

```sh
BREAKING CHANGE: <短い説明>

<詳細な説明 + マイグレーション手順>


Fixes #<issue番号>
```

### Deprecation（非推奨）

```sh
DEPRECATED: <非推奨になる機能の説明>

<詳細な説明 + 推奨される移行パス>


Closes #<PR番号>
```

---

## リバートコミット

前のコミットを revert する場合は `revert:` で始め、ヘッダーにリバート対象のコミットタイトルを続けます。

```sh
revert: <リバート対象のコミットヘッダー>

This reverts commit <SHA>.
<リバートする理由の説明>
```

---

## 例

### シンプルなバグ修正

```sh
fix(router): fix payload parameter in post request

Access first name parameter correctly from request payload.

Fixes #1234
```

### Breaking Change を含む新機能

```sh
feat(compiler): update template syntax for strict mode

Introduce stricter type checking in templates.

BREAKING CHANGE: templates now require explicit null checks.
Migration: replace `foo.bar` with `foo?.bar` where applicable.
```

### ドキュメントのみの変更

```sh
docs(changelog): update changelog to beta.5
```

---

> 参考: [Angular公式 commit-message-guidelines](https://github.com/angular/angular/blob/main/contributing-docs/commit-message-guidelines.md)
