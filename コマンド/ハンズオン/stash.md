# stash を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

作業中の変更を一時退避し、`apply`, `pop`, `drop` の違いを試します。

## シナリオ

作業途中で別ブランチを確認したくなった。

## 準備

```sh
mkdir git-command-practice-stash
cd git-command-practice-stash
git init -b main
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "base" > app.txt
git add app.txt
git commit -m "add base"

echo "work in progress" >> app.txt
echo "new file" > note.txt
```

## 試す

```sh
git status
git stash -u
git status
git stash list
```

stashを残したまま戻します。

```sh
git stash apply 'stash@{0}'
git status
```

もう一度退避して、戻しながらstash一覧から消します。

```sh
git stash -u
git stash pop 'stash@{0}'
git stash list
```

## 確認ポイント

- `apply` はstashを残します。
- `pop` は戻したあとstash一覧から消します。
- `-u` を付けると未追跡ファイルも退避します。

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-stash` フォルダを削除します。

## 実務での使いどころ

作業途中でブランチを切り替えたいとき、一時的に変更を横へ置きたいときに使います。

## 参考

- [Git公式 git-stash](https://git-scm.com/docs/git-stash)
