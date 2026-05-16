# cherry-pick を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

別ブランチにある1つのcommitだけを、今のブランチへ取り込みます。

## シナリオ

`feature-a` の修正のうち、便利な1commitだけを `main` に取り込みたい。

## 準備

```sh
mkdir git-command-practice-cherry-pick
cd git-command-practice-cherry-pick
git init
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "base" > app.txt
git add app.txt
git commit -m "add base"

git switch -c feature-a
echo "helper" > helper.txt
git add helper.txt
git commit -m "add helper"

echo "feature only" > feature.txt
git add feature.txt
git commit -m "add feature only"
```

## 試す

```sh
git log --oneline
```

`add helper` のcommit IDを控えて、`main` に戻って取り込みます。

```sh
git switch main
git cherry-pick <add-helperのcommit>
git log --oneline
```

## 確認ポイント

- `helper.txt` だけが `main` に取り込まれます。
- cherry-pick後のcommit IDは、元のcommitとは別になります。

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-cherry-pick` フォルダを削除します。

## 実務での使いどころ

別ブランチの一部修正だけを緊急で取り込みたいときに使います。

## 参考

- [Git公式 git-cherry-pick](https://git-scm.com/docs/git-cherry-pick)

