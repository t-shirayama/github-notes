# reflog を試す

この練習は、必ず練習用リポジトリで行います。

## 目的

`git reset --hard` で見えなくしたcommitを、`git reflog` から探して復元します。

## シナリオ

間違えてcommitを戻しすぎたが、直前のcommitを復活させたい。

## 準備

```sh
mkdir git-command-practice-reflog
cd git-command-practice-reflog
git init
git config user.name "Practice User"
git config user.email "practice@example.com"

echo "v1" > memo.txt
git add memo.txt
git commit -m "add v1"

echo "v2" >> memo.txt
git add memo.txt
git commit -m "add v2"

echo "v3" >> memo.txt
git add memo.txt
git commit -m "add v3"
```

## 試す

```sh
git log --oneline

# 直前のcommitを見えない状態にする
git reset --hard HEAD~1

git log --oneline
git reflog
```

`add v3` のcommit IDを `reflog` から探して、ブランチとして保護します。

```sh
git branch recover-v3 <commit>
git switch recover-v3
git log --oneline
```

## 確認ポイント

- `git log` から消えたcommitも、`git reflog` には残っていることがあります。
- 見つけたcommitは、すぐ `git branch <name> <commit>` で保護すると安心です。

## 片付け

```sh
cd ..
```

不要なら `git-command-practice-reflog` フォルダを削除します。

## 実務での使いどころ

reset、rebase、amendで「あれ、commitが消えた？」となったときの復旧調査に使います。

## 参考

- [Git公式 git-reflog](https://git-scm.com/docs/git-reflog)

