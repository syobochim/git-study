Git Study 02 2.1～2.4
=========

## Git リポジトリの取得

### 既存のディレクトリでのリポジトリの初期化

```sh
$ git init
```
.git という名前の新しいサブディレクトリが作られ、リポジトリに必要な
すべてのファイル(Git リポジトリのスケルトン)がその中に格納される

### 既存のリポジトリのクローン

```sh
$ git clone git://github.com/schacon/grit.git
# 別の名前でCloneする
$ git clone git://github.com/schacon/grit.git mygrit
```
checkoutではなくclone｡Gitはサーバーが保持しているデータをほぼ全てコピー｡


## 変更内容のリポジトリへの記録

- 変更内容のスナップショットをリポジトリにコミット｡
- 作業コピー内の各ファイルには追跡されている(tracked)ものと追跡されてない(untracked)ものの二通りがある｡
- ｢変更されていない(unmodified)」､「変更されている(modified)」､「ステージされている(staged)｣の三つの状態

### ファイルの状態の確認

クリーンな場合
```sh
$ git status
# On branch master
nothing to commit (working directory clean)
```
untrackedなファイルが存在する場合
```sh
$ vim README
$ git status
# On branch master
# Untracked files:
# (use "git add <file>..." to include in what will be committed) #
# README
nothing added to commit but untracked files present (use "git add" to track)
```

### 新しいファイルの追跡
```sh
$ git add README
```

### 変更したファイルのステージング
- ファイルを編集後再びgit addする｡
- ファイルの変更を自動で検知してくれないので手動でgit addする｡

### ファイルの無視
```sh
$ cat .gitignore
*.[oa]
```

- 空行あるいは# で始まる行は無視される
- 標準のglob パターンを使用可能
- ディレクトリを指定するには、パターンの最後にスラッシュ(/) をつける
- パターンを逆転させるには、最初に感嘆符(!) をつける

2.2.5 ステージされている変更/されていない変更の閲覧
- git diffを用いる｡
- 引数なしの場合は作業ディレクトリの内容とステージングエリアの内容を比較｡
- git diff ---cached(--staged)でステージされている変更と直近のコミットの内容を比較｡

```sh
$ git diff
diff --git a/benchmarks.rb b/benchmarks.rb
index 3cb747f..da65585 100644
--- a/benchmarks.rb
+++ b/benchmarks.rb
@@ -36,6 +36,10 @@ def main
@commit.parents[0].parents[0].parents[0]
end
+ run_code(x, 'commits 1') do
+ git.commits.size
+ end
+
run_code(x, 'commits 2') do
log = git.commits('master', 15)
log.size

$ git diff --cached
diff --git a/README b/README
new file mode 100644
index 0000000..03902a1
--- /dev/null
+++ b/README2
@@ -0,0 +1,5 @@
+grit
+ by Tom Preston-Werner, Chris Wanstrath
+ http://github.com/mojombo/grit
+
+Grit is a Ruby library for extracting information from a Git repository
```

### 変更のコミット

```sh
$ git commit
```
エディタには次のようなテキストが表示される｡

```sh
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
# new file: README
# modified: benchmarks.rb
~
~
~
".git/COMMIT_EDITMSG" 10L, 283C
```

オプション
```sh
$ git commit -m "Story 182: Fix benchmarks for speed"
$ git commit -am "Story 182: Fix benchmarks for speed"
```

### ファイルの削除
- ファイルをGitから削除するには､追跡対象をステージングエリアから削除しコミット｡
- globパターンをrmコマンドに渡せる｡

```sh
$ git rm grit.gemspec
rm 'grit.gemspec'
$ git status
# On branch master
#
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
# deleted: grit.gemspec
#
```
ステージングからファイルを削除したくない場合
```sh
$ git rm --cached readme.txt
```

###ファイルの移動
```sh
$ git mv README.txt README
```
下記と同義｡
```sh
$ mv README.txt README $ git rm README.txt
$ git add README
```

## コミット履歴の閲覧
git logコマンドを使用｡

```sh
$ git log
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date: Mon Mar 17 21:52:11 2008 -0700
changed the version number

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gee-mail.com>
Date: Sat Mar 15 16:40:33 2008 -0700
removed unnecessary test code

commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gee-mail.com>
Date: Sat Mar 15 10:31:28 2008 -0700
first commit
```
- -p: これは各コミットのdiffを表示。
- -2: 直近の 2 エントリだけを出力｡

```sh
$ git log ‒p -2
```

- --stat: 各コミットに関する統計情報を表示｡

```sh
$ git log --stat
```
- --pretty: フォーマットを指定して表示｡

```sh
$ git log --pretty=oneline
$ git log --pretty=format:"%h - %an, %ar : %s"
$ git log --pretty=format:"%h %s" --graph
```

### ログ出力の制限
様々な書式で動作｡
```sh
￼$ git log --since=2.weeks
```

###GUIによる歴史の可視化
gitk使え｡

## 作業のやり直し

### 直近のコミットの変更
```sh
$ git commit -m '初期コミット'
$ git add 忘れてたファイル
$ git commit --amend
```
ステージングエリアの内容をコミットに使用｡

### ステージしたファイルの取り消し
```sh
$ git reset HEAD benchmarks.rb benchmarks.rb: locally modified
```

### ファイルへの変更の取り消し
- 直近のコミットまで戻れる｡
- そのファイルが不要であることが確実にわかっているとき以外はこのコマンドを使わないように
- 単にファイルを片付けたいだけなら次の章で説明するstash やブランチがお薦め
- Gitにコミットした内容の全ては、ほほ常に取り消しが可能

```sh
$ git checkout -- benchmarks.rb
```







