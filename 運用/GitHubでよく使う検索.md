# GitHubでよく使う検索

GitHubの検索は、Issue、Pull Request、commit、ファイル、コードを探すときに便利です。よく使う条件を覚えておくと、調査やレビューが速くなります。

## 基本

| 探したいもの | 例 |
| ------------ | -- |
| リポジトリ内の文字列 | `repo:owner/repo keyword` |
| Issue | `repo:owner/repo is:issue keyword` |
| Pull Request | `repo:owner/repo is:pr keyword` |
| open中のPR | `repo:owner/repo is:pr is:open` |
| merge済みPR | `repo:owner/repo is:pr is:merged` |
| 自分が関係するもの | `repo:owner/repo involves:username` |

## PRとIssueを探す

| やりたいこと | 検索例 |
| ------------ | ------ |
| レビュー待ちPRを探す | `repo:owner/repo is:pr is:open review:none` |
| 自分にレビュー依頼されたPRを探す | `repo:owner/repo is:pr is:open review-requested:username` |
| label付きIssueを探す | `repo:owner/repo is:issue label:bug` |
| milestone付きIssueを探す | `repo:owner/repo milestone:"v1.0"` |
| 最近更新されたPRを探す | `repo:owner/repo is:pr sort:updated-desc` |

## commitを探す

commitタブや検索画面で、メッセージ、作者、SHAを手がかりに探します。

| やりたいこと | 検索例 |
| ------------ | ------ |
| 特定作者のcommit | `author:username` |
| commitメッセージで探す | `fix login` |
| 特定SHAを見る | SHAを検索欄やURLに貼る |

ローカルで探す場合は、次も使いやすいです。

```sh
git log --oneline --grep="keyword"
git log --author="name"
git show <commit>
```

## ファイルとコードを探す

| やりたいこと | 検索例 |
| ------------ | ------ |
| ファイル名で探す | `filename:README.md` |
| 拡張子で絞る | `extension:md keyword` |
| パスで絞る | `path:docs keyword` |
| 特定言語で探す | `language:Markdown keyword` |

GitHubのWeb画面では、リポジトリを開いて `t` を押すとファイル名検索ができます。コードを読む前に目的のファイルへ移動したいときに便利です。

## よく使う組み合わせ

```text
repo:owner/repo is:pr is:open label:docs
repo:owner/repo is:issue is:closed label:bug
repo:owner/repo is:pr is:merged "GitHub Actions"
repo:owner/repo extension:md "force-with-lease"
```

検索条件は一度で完璧に書くより、結果を見ながら条件を足していくと探しやすいです。
