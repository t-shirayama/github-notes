# Gitハンズオン

Gitコマンドを実際に試すためのシナリオ集です。

## 共通ルール

`reset --hard`, `clean -fd`, `rebase -i` などを安全に試すため、既存プロジェクトやこの備忘録リポジトリでは実行しません。必ず練習用リポジトリを作成して、その中で試します。

```sh
mkdir git-command-practice
cd git-command-practice
git init -b main
git config user.name "Practice User"
git config user.email "practice@example.com"
```

すでに同名フォルダがある場合は、`git-command-practice-2` など別名を使います。練習後に不要なら、フォルダごと削除して問題ありません。

## シナリオ一覧

| シナリオ | 試せること |
| -------- | ---------- |
| [reflog](./reflog.md) | 見えなくしたcommitを探して復元する |
| [cherry-pick](./cherry-pick.md) | 別ブランチの1commitだけ取り込む |
| [bisect](./bisect.md) | バグを入れたcommitを探す |
| [blame](./blame.md) | 行ごとの変更commitを追う |
| [worktree](./worktree.md) | 別ブランチを別ディレクトリで同時に開く |
| [range-diff](./range-diff.md) | 履歴整理前後のcommit列を比較する |
| [clean](./clean.md) | Git管理外ファイルの削除対象を確認する |
| [conflict](./conflict.md) | merge conflictを起こして解消する |
| [restore-reset](./restore-reset.md) | `restore` と `reset` の違いを試す |
| [revert](./revert.md) | commitを消さずに打ち消す |
| [stash](./stash.md) | 作業中の変更を退避する |
| [rebase-i](./rebase-i.md) | push前のcommit履歴を整理する |

## 片付け

練習用リポジトリが不要になったら、親フォルダへ戻って削除します。

```sh
cd ..
```

削除方法はOSやシェルによって異なります。必要なファイルがないことを確認してから削除します。
