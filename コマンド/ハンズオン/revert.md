# revert を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

commitを消さずに、反対の変更を入れるcommitで打ち消します。

## シナリオ

push済み想定のcommitを、安全に取り消したい。

## 準備

```sh
mkdir git-command-practice-revert
cd git-command-practice-revert
git init
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "safe=true" > app.txt
git add app.txt
git commit -m "add safe config"

echo "danger=true" >> app.txt
git add app.txt
git commit -m "add dangerous config"
```

## 試す

```sh
git log --oneline
git revert HEAD
git log --oneline
cat app.txt
```

## 確認ポイント

- 元のcommitは履歴に残ります。
- 取り消し用の新しいcommitが追加されます。
- 共有ブランチでは、履歴を書き換えない `revert` が扱いやすいです。

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-revert` フォルダを削除します。

## 実務での使いどころ

push済みの修正を、履歴を残したまま取り消したいときに使います。

## 参考

- [Git公式 git-revert](https://git-scm.com/docs/git-revert)

