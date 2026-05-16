# conflict を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

merge conflictをわざと起こし、解消と中止を試します。

## シナリオ

同じ行を `main` と `feature` の両方で変更して、Gitが自動でmergeできない状態を作ります。

## 準備

```sh
mkdir git-command-practice-conflict
cd git-command-practice-conflict
git init -b main
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "message=hello" > config.txt
git add config.txt
git commit -m "add config"

git switch -c feature
echo "message=hello from feature" > config.txt
git add config.txt
git commit -m "update message on feature"

git switch main
echo "message=hello from main" > config.txt
git add config.txt
git commit -m "update message on main"
```

## 試す

```sh
git merge feature
git status
cat config.txt
```

`config.txt` に入った `<<<<<<<`, `=======`, `>>>>>>>` を削除し、残したい内容に直します。

```sh
echo "message=hello from both" > config.txt
git add config.txt
git commit -m "merge feature"
git log --oneline --graph --decorate --all
```

中止も試したい場合は、別の練習用リポジトリで同じ準備をしてから次を実行します。

```sh
git merge feature
git merge --abort
git status
```

## 確認ポイント

- コンフリクトしたら、まず `git status` を見ます。
- merge中は解消後に `git add <file>` と `git commit` を実行します。
- やめたい場合は `git merge --abort` で中止できます。

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-conflict` フォルダを削除します。

## 実務での使いどころ

mainの取り込み、PRの更新、複数人で同じファイルを触ったときに使う基本対応です。

## 参考

- [Git公式 git-merge](https://git-scm.com/docs/git-merge)
- [Git公式 git-status](https://git-scm.com/docs/git-status)
