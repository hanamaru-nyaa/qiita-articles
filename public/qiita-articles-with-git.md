---
title: Qiitaの記事をGitHubで管理して、Claude Codeと一緒に書く
tags:
  - Qiita
  - QiitaCLI
  - GitHubActions
  - ClaudeCode
  - 初心者向け
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---
## はじめに

こんにちは、エンジニア2年目のはなまるです！

Qiitaの記事、ブラウザのエディタで書いていませんか？

私は、手元のエディタで書きたいと思っていました。
それに、不自然な日本語の修正や、学んだことからの記事のたたき台づくりを任せられるように、Claude Codeと連携しやすくしたかったんです。

そこで、Qiitaの記事をファイルにしてGitHubで管理することにしました。
この記事では、その環境の作り方と、Claude Codeと一緒に記事を書く流れをお話しします！

## 1. 全体の仕組み

登場するのは、Qiita CLI、GitHub、Claude Codeです。

| 道具 | やること |
|---|---|
| Qiita CLI | 記事をMarkdownファイルとして作る・プレビューする・Qiitaに投稿する |
| GitHub | 記事のファイルを置く。mainブランチにpushされたら、GitHub ActionsがQiitaに投稿する |
| Claude Code | 手元で記事のファイルを書く・直す |

つまり、**mainブランチにpushすると、そのままQiitaに投稿されます**。

## 2. 環境を作る

詳しい手順は、Qiita CLIの[公式README](https://github.com/increments/qiita-cli)に載っています。
Qiita CLIを使うには、Node.jsが必要です。

### Qiita CLIを入れる

記事を管理したいディレクトリで、次のコマンドを実行します。

```bash
npm install @qiita/qiita-cli --save-dev
```

続けて、初期化します。

```bash
npx qiita init
```

`.gitignore`、GitHub Actionsのワークフローのファイル、`qiita.config.json`が作られます。

### トークンを発行してログインする

Qiitaにログインして、[アクセストークンの発行ページ](https://qiita.com/settings/tokens/new)を開きます。
スコープは`read_qiita`と`write_qiita`を選びます。

発行したトークンを使って、Qiita CLIにログインします。

```bash
npx qiita login
```

トークンの入力を求められるので、貼り付けます。

### 投稿済みの記事を取り込む

次のコマンドで、投稿済みの記事をQiitaから取り込みます。

```bash
npx qiita pull
```

記事は`public`ディレクトリに、1記事1ファイルのMarkdownとして置かれます。
私の場合、ファイル名は`d0b3f04a076ebd02d1d8.md`のような記事のIDになっていました。

このままだと、どれがどの記事か分かりませんよね:crying_cat_face:
でも、Qiita CLIは記事をファイル名ではなく、ファイルの先頭に書かれた`id`で見分けています。
なので、ファイル名は内容が分かる名前に変えて大丈夫です。

### GitHubで投稿できるようにする

1. GitHubにリポジトリを作る
2. `https://github.com/[ユーザー名]/[リポジトリ名]/settings/secrets/actions`で、トークンを`QIITA_TOKEN`という名前のシークレットとして保存する
3. `npx qiita init`を実行したディレクトリをまるごとpushする

これで、mainブランチにpushするたびに、GitHub Actionsが記事をQiitaに投稿するようになります。

## 3. 記事を書いて公開するまで

### 下書きを作る

```bash
npx qiita new 記事のファイル名
```

`public/記事のファイル名.md`が作られます。
書き始める前に、ファイルの先頭にある`ignorePublish`を`true`にしておきましょう。
`true`にした記事は、pushしてもQiitaに投稿されません。

### プレビューで確認する

`npx qiita preview`を実行すると、`http://localhost:8888`で記事の見た目を確認できます。

### 公開する

1. `ignorePublish`を`false`にする
2. コミットして、mainブランチにpushする
3. GitHub Actionsが記事をQiitaに投稿する

投稿が終わると、Actionsが記事のファイルに`id`などを書き込み、「Updated by qiita-cli」というコミットを作る仕組みです。

## 4. Claude Codeと一緒に書く

記事がただのファイルになったおかげで、Claude Codeに書いたり直したりしてもらえるようになりました！
そう、これがやりたかったんです:smile_cat:

Claude Codeには、決まった作業の手順を書いておく「スキル」という仕組みがあります。
私は記事用のスキルを作って、書き方と公開までの手順をまとめています。
たとえば、書き方の部分はこんな感じです(抜粋)。

```markdown
- 書き出しは「こんにちは、エンジニア2年目のはなまるです！」
- です・ます体で、くだけた語り口
- 太字は、記事でいちばん伝えたい1〜2か所だけにする
- 本文は「。」のたびに改行する
```

手順のほうには、「下書きの間は`ignorePublish: true`にする」「pushする前には必ず了承を取る」などを書いています。
mainブランチにpushするとそのまま投稿されるので、勝手にpushされないようにしておくと安心です。

日本語の書き方は、プロジェクトの`CLAUDE.md`にも書いています。
日本語を自然にするスキル(natural-japanese)を使うことや、英語の前後に空白を入れないことなどです。

## 5. リポジトリをPublicにするときの注意

リポジトリをPublicにすると、pushしたファイルは誰でも読めるようになります。
下書きや、限定共有の記事も同じです。
公開前の記事や限定共有の記事を置くなら、Privateのままにしておきましょう。

## まとめ

Qiita CLIとGitHubで記事をファイルとして管理すると、手元のエディタで書けて、Claude Codeとも連携しやすくなります。
mainブランチにpushするとそのまま投稿されるので、下書きの間は`ignorePublish: true`にしておくのがポイントです。

ちなみに、この記事もClaude Codeと一緒に書きました！
