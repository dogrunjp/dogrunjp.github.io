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
$ pip install pelican
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
> Where do you want to create your new web site? [.] 
```

### pelicanブランチを作成しブランチを切り替える

```
$ git init
$ git checkout -b pelican
```

### 記事を書きmasterブランチに書き出す

```
$ vi content/hello.md
# もちろん何か書く

$ pelican content -o output -s pelicanconf.py
```

### pelicanブランチをGitHubにプッシュする

```
$ git add .
```

### masterブランチをGitHubにプッシュする

```
$ git checkout master
```
　
## 追記
自分の場合、記事をpushしてからGitHub Pagesに反映されるまで数分かかりました。pushしたのに何故？となるぐらいには時間がかかったことをメモしておきます。

## 参考
こちらの記事を参考にさせていただきました。

- [Python製Webサイト生成ツール Pelicanを使ってGithubにブログを公開する](https://qiita.com/akimach/items/dfcac164ac5669a6378e)