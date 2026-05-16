# blame を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

ファイルの各行が、どのcommitで変更されたかを確認します。

## シナリオ

設定ファイルの1行がいつ追加されたのかを調べたい。

## 準備

```sh
mkdir git-command-practice-blame
cd git-command-practice-blame
git init
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "timeout=30" > config.txt
git add config.txt
git commit -m "add timeout"

echo "retry=3" >> config.txt
git add config.txt
git commit -m "add retry"

echo "log_level=debug" >> config.txt
git add config.txt
git commit -m "add log level"
```

## 試す

```sh
git blame config.txt
```

気になる行のcommit IDを使って、変更内容を確認します。

```sh
git show <commit>
```

行範囲を絞る場合:

```sh
git blame -L 2,3 config.txt
```

## 確認ポイント

- `blame` は犯人探しではなく、変更の背景を追うために使います。
- commit IDが分かったら `git show` で差分とメッセージを確認します。

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-blame` フォルダを削除します。

## 実務での使いどころ

仕様の意図、設定値の追加理由、過去の判断を追いたいときに使います。

## 参考

- [Git公式 git-blame](https://git-scm.com/docs/git-blame)
- [Git公式 git-show](https://git-scm.com/docs/git-show)

