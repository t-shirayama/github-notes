# worktree を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

同じリポジトリの別ブランチを、別ディレクトリで同時に開きます。

## シナリオ

今のブランチを残したまま、`main` の状態を別フォルダで確認したい。

## 準備

```sh
mkdir git-command-practice-worktree
cd git-command-practice-worktree
git init
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "main" > app.txt
git add app.txt
git commit -m "add main file"

git switch -c feature
echo "feature" >> app.txt
git add app.txt
git commit -m "add feature change"
```

## 試す

```sh
git worktree add ../git-command-practice-worktree-main main
git worktree list
```

親フォルダに `git-command-practice-worktree-main` が作成され、そこでは `main` を確認できます。

```sh
cd ../git-command-practice-worktree-main
git status
cat app.txt
```

## 確認ポイント

- 1つのリポジトリから、複数の作業ディレクトリを作れます。
- 同じブランチを複数worktreeで同時にcheckoutすることはできません。

## 片付け

```sh
cd ../git-command-practice-worktree
git worktree remove ../git-command-practice-worktree-main
git worktree list
cd ..
```

不要なら `git-command-practice-worktree` フォルダを削除します。

## 実務での使いどころ

作業中の変更を残したまま、別ブランチの確認や緊急修正をしたいときに使います。

## 参考

- [Git公式 git-worktree](https://git-scm.com/docs/git-worktree)

