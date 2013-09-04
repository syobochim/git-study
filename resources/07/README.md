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

![1](./img/1.png)

そんな場合は
```sh
$ ssh user@git.example.com $ cd /opt/git/my_project.git
$ git init --bare --shared
```



