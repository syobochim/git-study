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
