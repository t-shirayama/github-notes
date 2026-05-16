# range-diff を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

履歴整理前後のcommit列を比較します。

## シナリオ

PRのcommitを整理したあと、意図しない変更が混ざっていないか確認したい。

## 準備

```sh
mkdir git-command-practice-range-diff
cd git-command-practice-range-diff
git init
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "base" > app.txt
git add app.txt
git commit -m "add base"

git switch -c feature-before
echo "one" >> app.txt
git add app.txt
git commit -m "add one"

echo "two" >> app.txt
git add app.txt
git commit -m "add two"

git switch -c feature-after
git commit --amend -m "add two with better message"
```

## 試す

```sh
git range-diff main..feature-before main..feature-after
```

## 確認ポイント

- commit列の対応関係を見られます。
- rebaseやamend後に、レビュー差分の見直しに使えます。

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-range-diff` フォルダを削除します。

## 実務での使いどころ

rebase後のPRで「何が変わったか」をcommit単位で確認したいときに使います。

## 参考

- [Git公式 git-range-diff](https://git-scm.com/docs/git-range-diff)

