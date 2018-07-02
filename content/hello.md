Title: Hello, pelican
Date: 2018-07-01 21:58
Category: Test

# PelicanでGitHub Pagesのブログを作成・管理する方法

どこかにコードやサーバの設定手順を残しておく場所が欲しかったため、
GitHub PageをPelicanで管理することにしました。その手順を最初の記事としてアップしてみます。

## 環境設定
今回自分は開発端末にPelican用のローカルリポジトリを作るため、pyenvで専用の環境を作ります。端末はmacOS 10.12です。

```
$ pyenv virtualenv 3.5.0 github-page
$ pyenv local github-page
```
この環境にペリカンに必要な諸々をインストールします。

```
$ pip install pelican$ pip install Markdown$ pip install typogrify$ pip install ghp-import
```

## 記事の作成

### GitHub Page用のレポジトリを作成する
GitHubで新規に"githubのアカウント名.github.io"のレポジトリを作成します。

Pelicanではmasterブランチとpelicanブランチを利用しますが各ブランチの役割は

- pelicanブランチ：記事を作成しmasterブランチにアセットを格納する
- masterブランチ：ここからGitHubに記事をpushする

のように分けられます。

### ローカル環境にPelicanを設定する

```
$ mkdir dogrunjp.github.io # ローカルのリポジトリ名はなんでも良さそう
$ cd ./dogrunjp.github.io
$ pelican-quickstart
```

ここから対話方式の設定が始まります。dogrunjp.github.ioの場合はこんな↓感じ。

```
> Where do you want to create your new web site? [.] > What will be the title of this web site? Inugoya Blog> Who will be the author of this web site? oec> What will be the default language of this web site? [ja] ja> Do you want to specify a URL prefix? e.g., http://example.com   (Y/n) y> What is your URL prefix? (see above example; no trailing slash) https://dogrunjp.github.io> Do you want to enable article pagination? (Y/n) y> How many articles per page do you want? [10] > What is your time zone? [Europe/Paris] Asia/Tokyo> Do you want to generate a Fabfile/Makefile to automate generation and publishing? (Y/n) y> Do you want an auto-reload & simpleHTTP script to assist with theme and site development? (Y/n) Y> Do you want to upload your website using FTP? (y/N) N> Do you want to upload your website using SSH? (y/N) N> Do you want to upload your website using Dropbox? (y/N) N> Do you want to upload your website using S3? (y/N) N> Do you want to upload your website using Rackspace Cloud Files? (y/N) N> Do you want to upload your website using GitHub Pages? (y/N) y> Is this your personal page (username.github.io)? (y/N) y
```

### pelicanブランチを作成しブランチを切り替える

```
$ git init$ git remote add origin git@github.com:dogrunjp/dogrunjp.github.io.git
$ git checkout -b pelican
```

### 記事を書きmasterブランチに書き出す

```
$ vi content/hello.md
# もちろん何か書く

$ pelican content -o output -s pelicanconf.py$ ghp-import output -b master
```

### pelicanブランチをGitHubにプッシュする

```
$ git add .$ git commit -m "Initial Commit"$ git push origin pelican
```

### masterブランチをGitHubにプッシュする

```
$ git checkout master$ git add .$ git commit -m "Publish"$ git push origin master
```
　
## 追記
自分の場合、記事をpushしてからGitHub Pagesに反映されるまで数分かかりました。pushしたのに何故？となるぐらいには時間がかかったことをメモしておきます。

## 参考
こちらの記事を参考にさせていただきました。

- [Python製Webサイト生成ツール Pelicanを使ってGithubにブログを公開する](https://qiita.com/akimach/items/dfcac164ac5669a6378e)- [Hello, world! bonohu blog](https://bonohu.github.io/hello-world.html)- [Pelican + Markdown + GitHub Pagesで管理するブログの作り方](https://blog.sotm.jp/2014/01/04/Pelican-Markdown-GithubPages-install-guide/)
