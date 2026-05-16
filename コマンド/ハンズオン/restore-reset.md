# restore / reset を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

`git restore`, `git restore --staged`, `git reset --soft/--mixed/--hard` の違いを試します。

## シナリオ

作業中の変更、ステージ済み変更、commit済み変更をそれぞれ戻します。

## 準備

```sh
mkdir git-command-practice-restore-reset
cd git-command-practice-restore-reset
git init -b main
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "v1" > app.txt
git add app.txt
git commit -m "add v1"
```

## 試す

作業中の変更を戻します。

```sh
echo "work in progress" >> app.txt
git diff
git restore app.txt
git status
```

ステージ済みの変更を外します。

```sh
echo "staged change" >> app.txt
git add app.txt
git status
git restore --staged app.txt
git status
git restore app.txt
```

commitを取り消します。

```sh
echo "v2" >> app.txt
git add app.txt
git commit -m "add v2"

# commitだけ取り消し、変更はステージ済みで残す
git reset --soft HEAD~1
git status

git commit -m "add v2 again"

# commitを取り消し、変更は作業ツリーに残す
git reset --mixed HEAD~1
git status

# 作業ツリーも戻す
git reset --hard HEAD
git status
```

## 確認ポイント

- `restore` は主に作業ツリーやステージを戻すときに使います。
- `reset --soft` はcommitだけ戻します。
- `reset --hard` は未commit変更も消すため、練習用リポジトリ以外では慎重に扱います。

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-restore-reset` フォルダを削除します。

## 実務での使いどころ

commit前後の取り消し判断に使います。

## 参考

- [Git公式 git-restore](https://git-scm.com/docs/git-restore)
- [Git公式 git-reset](https://git-scm.com/docs/git-reset)
