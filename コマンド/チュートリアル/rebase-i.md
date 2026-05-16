# rebase -i を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

push前のcommit履歴を、`reword` と `squash` で整理します。

## シナリオ

PRを出す前に、細かすぎるcommitをまとめ、commitメッセージを直したい。

## 準備

```sh
mkdir git-command-practice-rebase-i
cd git-command-practice-rebase-i
git init
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "base" > app.txt
git add app.txt
git commit -m "add base"

echo "one" >> app.txt
git add app.txt
git commit -m "wip one"

echo "two" >> app.txt
git add app.txt
git commit -m "wip two"
```

## 試す

```sh
git log --oneline
git rebase -i HEAD~2
```

エディタが開いたら、例として次のように変更します。

```txt
reword <commit> wip one
squash <commit> wip two
```

保存すると、commitメッセージを編集する画面が開きます。分かりやすいメッセージに直します。

```sh
git log --oneline
```

## 確認ポイント

- `reword` はcommitメッセージを変更します。
- `squash` は前のcommitにまとめます。
- `rebase -i` は履歴を書き換えるため、push前の個人ブランチで使います。

## 中止する

rebase中にやめたい場合:

```sh
git rebase --abort
```

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-rebase-i` フォルダを削除します。

## 実務での使いどころ

PR前にcommit履歴を読みやすく整えたいときに使います。

## 参考

- [Git公式 git-rebase](https://git-scm.com/docs/git-rebase)

