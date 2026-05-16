# bisect を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

どのcommitで壊れたかを `git bisect` で探します。

## シナリオ

`app.txt` に `broken` が入ったcommitを探したい。

## 準備

```sh
mkdir git-command-practice-bisect
cd git-command-practice-bisect
git init
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "status=ok" > app.txt
git add app.txt
git commit -m "app works"

echo "feature=one" >> app.txt
git add app.txt
git commit -m "add feature one"

echo "status=broken" > app.txt
git add app.txt
git commit -m "break app"

echo "feature=two" >> app.txt
git add app.txt
git commit -m "add feature two"
```

## 試す

```sh
git bisect start
git bisect bad
git bisect good HEAD~3
```

Gitが途中のcommitへ移動するので、ファイルを確認して判定します。

```sh
cat app.txt

# broken がなければ
git bisect good

# broken があれば
git bisect bad
```

原因commitが表示されたら終了します。

```sh
git bisect reset
```

## テストコマンドで自動判定する

`git bisect` は、Gitが確認対象のcommitへ移動します。そのcommitが正常か壊れているかは、人が動作確認するか、テストコマンドで判定します。

テストコマンドで自動判定する場合は、`git bisect run` を使います。

```sh
git bisect start
git bisect bad
git bisect good HEAD~3

# broken が含まれていたら失敗するコマンドを実行
git bisect run grep -q "status=ok" app.txt

git bisect reset
```

実務では、次のようにテストやチェック用スクリプトを指定することが多いです。

```sh
git bisect run npm test
git bisect run npm run test:e2e
git bisect run ./scripts/check.sh
```

## 確認ポイント

- `good` は正常だったcommit、`bad` は壊れているcommitです。
- `git bisect run` は、指定したコマンドの終了コードで `good` / `bad` を自動判定します。
- 終了コード `0` は `good`、失敗の終了コードは `bad` として扱われます。
- 調査が終わったら必ず `git bisect reset` します。

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-bisect` フォルダを削除します。

## 実務での使いどころ

いつ壊れたか分からない不具合を、commit単位で絞り込むときに使います。

## 参考

- [Git公式 git-bisect](https://git-scm.com/docs/git-bisect)
