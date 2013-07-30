Git Study 02 2.5～2.8
=========

## 2.5 リモートでの作業

リモートリポジトリ・・・  
インターネット上あるいはその他ネットワーク上のどこかに存在するプロジェクトこと  
※このgit-studyもしかり  

今回はこのリモートリポジトリを管理する話
- リモートリポジトリの追加
- 不要になったリモートリポジトリの削除
- リモートブランチの管理や追跡対象/追跡対象外の設定

### リモートの表示
設定しているリモートサーバーをみる
```sh
$ git remote
```

-v を指定すると、その名前に対応するURLが見える。
```sh
$ git remote -v
```

### リモートの追加
長いURLを毎回打ち込んでいたら大変 => 新しいリモートGitリポジトリに名前を付ける
```sh
$ git remote add [shortname] [url]
```

### リモートからのフェッチとプル
Clone元が持つ情報の中で、自分のリポジトリにまだ存在しない情報を取得したい場合
やり方２つ
- git fetch
- git pull

#### git fetch [shortname]
- リモートからデータを取ってくる。
- ただし取ってくるだけで、それ以外は何もしない。
- つまり、名もないブランチ（origin/master）としてローカルに保存されるのみで、マージしてくれない。

マージしたい場合は以下のコマンドを実行。
```sh
$ git merge origin/master
```

#### git pull [shortname]
上の
```sh
$ git fetch
```
と
```sh
$ git merge origin/master
```
をいっぺんにやる。らくちん。

### リモートへのプッシュ
修正したらaddしてcommitして ==> pushすべし!!

originサーバーにプッシュしたい場合
```sh
$ git push origin master
```

### リモートの調査
特定のリモート情報の詳細を知りたい場合
```sh
$ git remote show origin
```

### リモートの削除・リネーム
リモートを参照する名前を変えたい場合
```sh
$ git remote rename [before] [after]
```

リモートの参照を削除したい場合
```sh
$ git remote rm [name]
```

## 2.6 タグ
歴史上の重要なポイントに印をつけることができる。

### タグの一覧表示
```sh
$ git tag
v0.1
v1.3
```

パターンを指定してタグを検索する  
※1.4.2系のタグのみ見たい場合
```sh
$ git tag -l 'v1.4.2.*'
```

### タグの作成
Gitには以下の２通りの作成法がある。
- 軽量（lightweight）版  
  変更のないブランチ。特定のコミットに対する単なるポイントでしかない。
- 注釈（annotated）版  
  Gitデータベース内に完全なオオブジェクトとして格納される。  
  チェックサムが付き、タグを作成した人の名前・メアド・作成日時・タグ付け時のメッセージなどが含まれる。

#### 注釈版タグ
```sh
$ git tag -a v1.4 -m 'message'
$ git
v0.1
v1.3
v1.4
```

タグのデータとそれに関連づけられたコミットを見る
```sh
$ git show
```

#### 署名付きタグ
上記の-aを-sにかえるだけ。
```sh
$ git tag -s v1.5 -m 'message'
```

#### 軽量版タグ
なんもオプションをつけない
```sh
$ git tag v1.5
```

### タグの検証
署名付きタグの検証を行うとき
```sh
$git tag -v [tag-name]
```
※時間があったら探る

### 後からタグ付け
過去にさかのぼってたグ付けしたい場合

例えば、以下のコミットログの「add folder 03」にタグ付けしたい場合
```sh
$git log --pretty=oneline
9f4d173d985a70a9ce339d3efa2f33869142d44b update readme
2294918a902b6203263a4b5e325f7c9886aaf1b7 update readme
f7f728eac78776e27d215212e78ef8ed59ae6654 update 03/readme
8a39ce10e3f7c49a21350713dbd2133172d629ce Merge pull request #4 from uggds/master
05bfef1ba9b8e2766f47de634ceffeec61acc6e6 update test.txt for fork lesson
3b0132abb669a03d491aae2dc4b68c1d1345b2cc Merge pull request #3 from syobochim/master
539a805f4a5c76459466cbfb824c82cdd7eacc02 add folder 03
5088dcfa51def83f1ff929838c55d7d74e4926d6 02回目用資料完成
6c67a20d76a7103c6d371f4fc9e2ad5c1d7c2b54 update 02/readme
1ab95f1dfc182ce917b9e25ee580b140915e0a26 delte slide.
61d002a0052a82deb4519d6930a5a60d58bf02ed move slide to resource.
a134dedf545985fc0b91075fd4cabaf951cb8519 Update README.md
5679a7e33086cd99eafef434aa8ed66cf31438b6 make slide dirctory
e5e482a4168efc8f242317d0fd1938d133c658b6 add slide.
79f1b7dacdaac0a5e13746b7bf71d520028aa11a 第一回に向けて説明を追加｡
6dc1d2745fd468c3108266ead601ef43384d9e4a Initial commit
```

そのコミットのチェックサム (あるいは その一部) をコマンドの最後に指定する。
```sh
$git tag -a v3.0 4c82cdd7eacc02
```

### タグの共有
タグを作ったら、タグをリモートサーバーにプッシュするよう明示する必要がある  
リモートブランチを共有するときと似ている。

```sh
$git push origin [tagname]
```

複数のタグを一度にプッシュしたいときは
```sh
$git push origin --tags
```
これは、手元にあるタグのうちリモートサーバに存在しないものすべてを転送。


## 2.7 裏の技
### 自動補完
```sh
$ls /usr/local/git/contrib/completion
git-completion.bash git-completion.zsh git-prompt.sh
```
にあるgit-completion.bashをホームディレクトリにコピーして実行する。
```sh
$cp /usr/local/git/contrib/completion/git-completion.bash ~
$source ~/git-completion.bash
```
Git コマンドの入力中にタブキーを押せば、補完候補があらわれて選択できるようになる。
```sh
$git co<tab><tab> 
commit config
```

