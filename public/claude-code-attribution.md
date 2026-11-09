---
title: GitHubのコミットにClaudeのアイコンが並ぶのはなぜ？Co-Authored-Byの正体とattribution設定での消し方
tags:
  - ClaudeCode
  - Git
  - GitHub
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

Claude Codeにコミットを任せていると、GitHubのコミット一覧に見慣れないアイコンが並んでいることに気づきませんか？

自分のリポジトリなのに、隣にClaudeのアイコンが……:scream_cat:

私は正直、邪魔なので消したい派です。

この記事では、このアイコンがなぜ並ぶのかと、Claude Codeの`attribution`設定で消す方法をお話しします！

## 1. コミットやPRに入る文言

Claude CodeがコミットやPRを作ると、次の文言が入ります。

| 対象 | 入る文言 |
|---|---|
| コミットメッセージ | `Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>`(モデル名はそのとき使っているもの) |
| PRの説明文 | `🤖 Generated with [Claude Code](https://claude.com/claude-code)` |

私の記事管理用のリポジトリでも、最初のコミットにしっかり入っていました。

```
Initial commit: import Qiita articles with Qiita CLI

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

## 2. この1行は誰が書いて、誰が表示しているのか

### 書いているのはClaude Code

Claude Codeには、コミットやPRを作るときにこの文言を付ける、という指示があらかじめ組み込まれています。
gitのフックなどで後から追加しているわけではありません。
Claude Codeがメッセージを書く時点で、末尾に書いています。

私が確かめたときも、Claude Codeが受け取っている指示の中に「コミットメッセージの末尾に`Co-Authored-By: ...`を付ける」と書かれていました。

### アイコンを出しているのはGitHub

GitHubは`Co-Authored-By:`の行を読み取り、メールアドレスがGitHubアカウントに紐づいていれば、そのアカウントを共著者として表示します。
そのため、コミット一覧にClaudeのアイコンが並ぶわけです。

つまり、**文言を書いているのはClaude Codeで、アイコンを出しているのはGitHub**です。

## 3. attribution設定で消す

`attribution`は、Claude CodeがコミットメッセージやPRの説明文に付ける文言を指定する設定です。
Claude Codeの設定ファイル`settings.json`に書きます。

`~/.claude/settings.json`に次のように書くと、Claude Codeはこれらの文言を付けなくなります。

```json:~/.claude/settings.json
{
  "attribution": {
    "commit": "",
    "pr": ""
  }
}
```

それぞれのキーの意味はこちらです。

| キー | 意味 | 空文字にすると |
|---|---|---|
| `commit` | コミットメッセージに付ける文言(`Co-Authored-By:`の行を含む) | 何も付かない |
| `pr` | PRの説明文に付ける文言 | 何も付かない |

キーを書かないと、デフォルトの文言が付きます。
消したいときは、キーを書いたうえで空文字にします。

## 4. そもそも消していいの？

消してよいかは、プロジェクトの方針で決まります。
OSSや会社によっては、AIを使ったことを明記するよう求めたり、勧めたりしています。

なので、選べるのは「消す」だけではありません。

- 付けたままにする
- 文言を変える(`"commit": "Assisted-by: Claude Code"`など)
- 消す

チームやプロジェクトのルールを確認してから決めましょう！

## まとめ

コミット一覧に並ぶClaudeのアイコンは、Claude Codeが書いた`Co-Authored-By:`の行をGitHubが読み取って表示したものでした。
`attribution`設定を使えば、この文言を消すことも、好きな文言に変えることもできます。

ちなみに、消したい派の私はもちろん空文字にしました！
