# clean を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

Git管理外ファイルの削除対象を、削除前に確認します。

## シナリオ

ビルド生成物や一時ファイルを削除したい。

## 準備

```sh
mkdir git-command-practice-clean
cd git-command-practice-clean
git init -b main
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "tracked" > app.txt
git add app.txt
git commit -m "add tracked file"

echo "temp" > temp.log
mkdir dist
echo "build" > dist/output.txt
```

## 試す

```sh
git status

# 削除対象を確認するだけ
git clean -n

# ディレクトリも含めて確認するだけ
git clean -nd
```

確認した対象を削除します。

```sh
git clean -fd
git status
```

## 確認ポイント

- `git clean -n` は削除せず、対象だけ表示します。
- `git clean -fd` はGit管理外ファイルを削除します。戻せないことが多いので注意します。

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-clean` フォルダを削除します。

## 実務での使いどころ

ビルド生成物や一時ファイルを消して、作業ツリーを整理したいときに使います。

## 参考

- [Git公式 git-clean](https://git-scm.com/docs/git-clean)
