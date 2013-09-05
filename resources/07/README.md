Git Study 07回 4.2~4.10
=========
## 4.2 サーバー用のGitの取得

### Gitサーバーを立ち上げる  

#### ①ベアリポジトリ（作業ディレクトリを持たないリポジトリ）を作る  
  
既存のリポジトリmy_projectがある場合
```sh
$ git clone --bare my_project my_project.git
initialized empty Git repository in /opt/projects/my_project.git/
```
これはおおざっぱに言うと以下と同じこと
```sh
$ cp -Rf my_project/.git my_project.git
```

#### ②ベアリポジトリをサーバー上へ設置  
  
git.example.comというサーバーにSSHでアクセスできる場合  
サーバー上の/opt/gitディレクトリに.gitを置く
```sh
$ scp -r my_project.git user@git.example.com:/opt/git
```

この時点でSSHでアクセスできて、/opt/gitディレクトリへの読み込みアクセス権限がある人なら
```sh
$ git clone user@git.example.com:/opt/git/my_preject.git
```
さらに/opt/git/.my_project.gitへの書き込み権限がある人なら、すでにプッシュが可能！

か、書き込み権限がないよー  
![1](./img/1.png)

そんな場合は
```sh
$ ssh user@git.example.com $ cd /opt/git/my_project.git
$ git init --bare --shared
```

てゆかそもそもSSHアクセスできないよー  
![1](./img/1.png)

## 4.3 SSH公開鍵の作成

まずは自分がすでに公開鍵を持っていないかを確認する  
各ユーザーのSSH鍵はそのユーザーの~/.sshディレクトリに置かれているので、その中を調べる  
```sh
$ cd ~/.ssh
$ ls
authorized_keys2 id_dsa known_hosts config id_dsa.pub
```
「○○」と「○○.pub」がある場合  
=> .pubがつく方が公開鍵  
  
ない（そもそも.sshディレクトリがない）場合  
=>ssh-keygenを実行して、それを作る

```sh
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/schacon/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/schacon/.ssh/id_rsa.
Your public key has been saved in /Users/schacon/.ssh/id_rsa.pub.
The key fingerprint is:
43:c5:5b:5f:b1:f1:50:43:ad:20:a6:92:6a:1f:9a:3a schacon@agadorlaptop.local
```

鍵の保存先を指定するよう求められたら => .ssh/id_da  
パスワードを入力するよう求められたら => ２回入力（入力したくない場合は空を指定）

## 4.3 サーバーのセットアップ

authorized_keys方式でユーザー認証を行う  
サーバーにはUbuntuのようなLinuxディストリビューションを動かしていると仮定
  
①'git'ユーザーを作成  
②'git'の.sshディレクトリを作成
```sh
$ sudo adduser git
$ su git
$ cd
$ mkdir .ssh
```

③開発者たちがSSH公開鍵を'git'のauthorized_keysに追加する  
※開発者たち（John, Josie, Jessica）
```sh
$ cat/tmp/id_rsa.john.pub >> ~/.ssh/authorized_keys
$ cat/tmp/id_rsa.josie.pub >> ~/.ssh/authorized_keys
$ cat/tmp/id_rsa.jessica.pub >> ~/.ssh/authorized_keys
```

④John, Josie, Jessicaが使うための空のリポジトリを作成
```sh
$ cd /opt/git
$ mkdir project.git
$ cd project.git
$ git --bare init
```

⑤これでJohn, Josie, Jessicaは'git'ユーザーとしてプロジェクトの最初のバージョンをプッシュできるようなった！  
'git'ユーザーとリポジトリを作ったサーバーのホスト名をgitserverとして、  
そのサーバーを指すようにしてDNSを設定しておくと、以下のコマンドが使える。
```sh
# Johnのコンピューターで
$ cd myproject
$ git init
$ git add .
$ git commit -m 'initial commit'
$ git remote add origin git@gitserver:/opt/git/project.git
$ git push origin master
```

⑥万が一に備えて'git'ユーザーができることを制限する  
```sh
$ sudo vim/etc/passwe
```
一番最後に、このような行があるはず
```sh
git:x:1000:1000::/home/git:/bin/sh
```
こいつを以下に変更する
```sh
git:x:1000:1000::/home/git:/usr/bin/git-shell
```
git-shell … Gitに付属しているGitに関する作業しかできない制限付きシェル

## 4.5 一般公開

オープンソースのプロジェクトを扱ったり、自動ビルドやCI用に大領のサーバーが用意されていて入れ替わりが  
しばしば発生する場合など、匿名アクセスのためだけに、毎回SSH鍵を作成しなければならないのは大変。  
  
一番シンプルな方法は、ウェブサーバーを立ち上げてそのドキュメントルートにGitリポジトリを置き、  
post-update フックを有効にすること。  

例：リポジトリが/opt/git ディレクトリにあり、マシン上でApacheが稼働中であるものとする。  

①フックを有効にする
```sh
$ cd project.git
$ mv hooks/post-update.sample hooks/post-update $ chmod a+x hooks/post-update
```
post-update は、いったい何をしているか中身をみると
```sh
$ cat .git/hooks/post-update #!/bin/sh
exec git-update-server-info
```
SSH 経由でサーバーへのプッシュが行われると、Git はこのコマンドを実行し、HTTP 経由での取得に必要なファイルを更新する。
  
②
Apache の設定に VirtualHost エントリを追加して Git プロジェクトのディレクトリをドキュメントルートに設定する。
```sh
<VirtualHost *:80>
  ServerName git.gitserver
  DocumentRoot /opt/git
  <Directory /opt/git/>
    Order allow, deny
    allow from all
  </Directory>
</VirtualHost>
```
③/
opt/git ディレクトリの所有グループを www-data にし、ウェブサーバがこのリポジトリにアクセスできるようにする。

```sh
$ chgrp -R www-data /opt/git
```
④pacheを再起動
プロジェクトの URL を指定してリポジトリのクローンができるようになる。

```sh
$ git clone http://git.gitserver/project.git
```
