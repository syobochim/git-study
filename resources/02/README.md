Git勉強会 02回 2.1～2.4
=========

2.1 Git リポジトリの取得

2.1.1 既存のディレクトリでのリポジトリの初期化

$ git init
.git という名前の新しいサブディレクトリが作られ、リポジトリに必要な
すべてのファイル(Git リポジトリのスケルトン) がその中に格納されます

2.1.2 既存のリポジトリのクローン

$ git clone git://github.com/schacon/grit.git

別の名前でCloneする
$ git clone git://github.com/schacon/grit.git mygrit

2.2.1 ファイルの状態の確認
$ git status
# On branch master
nothing to commit (working directory clean)

$ vim README
$ git status
# On branch master
# Untracked files:
# (use "git add <file>..." to include in what will be committed)
#
# README
nothing added to commit but untracked files present (use "git add" to track)

2.2.2 新しいファイルの追跡
$ git add README

2.2.3 変更したファイルのステージング
ファイルの変更を自動で検知してくれないので手動でgit addする事
ショートカット
$ git commit -am "your messsage"

2.2.4 ファイルの無視
$ cat .gitignore
*.[oa]

• 空行あるいは# で始まる行は無視される
• 標準のglob パターンを使用可能
• ディレクトリを指定するには、パターンの最後にスラッシュ(/) をつける
• パターンを逆転させるには、最初に感嘆符(!) をつける

2.2.5 ステージされている変更/ されていない変更の閲覧

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
git

2.2.6 変更のコミット


$ git commit


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


$ git commit -m "Story 182: Fix benchmarks for speed"
[master]: created 463dc4f: "Fix benchmarks for speed"
2 files changed, 3 insertions(+), 0 deletions(-)
create mode 100644 README

2.2.8 ファイルの削除
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

$ git rm --cached readme.txt

2.3 コミット履歴の閲覧

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

$ git log ‒p -2

$ git log --stat

$ git log --pretty=oneline

$ git log --pretty=format:"%h - %an, %ar : %s"

$ git log --pretty=format:"%h %s" --graph


2.4 作業のやり直し
2.4.1 直近のコミットの変更

$ git commit --amend

$ git commit -m '初期コミット'
$ git add 忘れてたファイル
$ git commit --amend

2.4.2 ステージしたファイルの取り消し

$ git reset HEAD benchmarks.rb
benchmarks.rb: locally modified
$ git status
# On branch master
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
# modified: README.txt
#
# Changes not staged for commit:
# (use "git add <file>..." to update what will be committed)




2.4.3 ファイルへの変更の取り消し

$ git checkout -- benchmarks.rb
$ git status
# On branch master
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
# modified: README.txt
#







