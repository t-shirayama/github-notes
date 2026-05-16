# コマンド

Gitコマンドを用途別にまとめています。

## 用途別リンク

| ファイル | 主な内容 |
| -------- | -------- |
| [日常操作](./日常操作.md) | `status`, `diff`, `add -p`, `commit`, `origin`, `pull`, `fetch`, `log`, `show` |
| [ブランチ操作](./ブランチ操作.md) | `switch`, `checkout`, `branch`, バックマージ, `merge`, `rebase`, `stash` |
| [コンフリクト解消](./コンフリクト解消.md) | `status`, conflict marker, `add`, `merge --abort`, `rebase --continue` |
| [取り消し操作](./取り消し操作.md) | `restore`, `reset`, `revert`, `commit --amend`, push前後の判断 |
| [履歴の書き換え](./履歴の書き換え.md) | `reset --hard`, `rebase -i`, `push --force-with-lease`, orphanブランチ |
| [差がつくコマンド](./差がつくコマンド.md) | `reflog`, `cherry-pick`, `bisect`, `blame`, `worktree`, `range-diff`, `clean -n` |
| [チュートリアル](./チュートリアル/README.md) | 練習用リポジトリで試すシナリオ集 |
| [困ったとき](./困ったとき.md) | `status`, `log`, `reflog`, 危険操作の前に確認すること |

## 迷ったときの早見表

| やりたいこと | まず使うコマンド | 参照 |
| ------------ | ---------------- | ---- |
| 今の状態を確認したい | `git status` | [日常操作](./日常操作.md#状態を確認する) |
| 何をすればいいか分からない | `git status` | [困ったとき](./困ったとき.md) |
| 差分を確認したい | `git diff` | [日常操作](./日常操作.md#差分を確認する) |
| ブランチを切り替えたい | `git switch <branch>` | [ブランチ操作](./ブランチ操作.md#ブランチを切り替える) |
| `origin` の意味を知りたい | `git remote -v` | [日常操作](./日常操作.md#origin-とは) |
| mainの変更を作業ブランチへ取り込みたい | `git merge main` または `git rebase origin/main` | [ブランチ操作](./ブランチ操作.md#バックマージと-rebase-の違い) |
| コンフリクトを解消したい | `git status` | [コンフリクト解消](./コンフリクト解消.md) |
| 実際にコマンドを試したい | 練習用リポジトリを作る | [チュートリアル](./チュートリアル/README.md) |
| 作業中の変更を一時退避したい | `git stash -u` | [ブランチ操作](./ブランチ操作.md#作業中の変更を一時退避する) |
| 作業中の修正を一度リセットしたい | `git restore .` または `git reset --hard HEAD` | [取り消し操作](./取り消し操作.md#作業中の修正を一度リセットする) |
| push前のコミットを取り消したい | `git reset` | [取り消し操作](./取り消し操作.md#push前のコミットを取り消す) |
| push済みの修正を安全に取り消したい | `git revert <commit>` | [取り消し操作](./取り消し操作.md#push済みの修正を安全に取り消す) |
| push済みの履歴を削除したい | `git reset --hard` + `git push --force-with-lease` | [履歴の書き換え](./履歴の書き換え.md#push済みの修正を履歴ごと削除する) |
| 消したコミットを探したい | `git reflog` | [差がつくコマンド](./差がつくコマンド.md#reflog-で過去の移動履歴を見る) |
