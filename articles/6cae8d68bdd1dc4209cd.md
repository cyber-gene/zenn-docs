---
title: "いつかサーバーを閉じるとき 〜お金をかけずに 410 を返す方法〜"
emoji: "🌐"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Fediverse", "Mastodon", "Netlify"]
published: true
---

この記事は [Fediverse Advent Calendar 2020](https://adventar.org/calendars/6046) 9日目の記事です。

昨日の記事は、Fediverse でおなじみのコーヒー豆屋さんNelson Coffee Roasterさんの仙台でおすすめのお店記事でした。
Nelsonさんとこからは、僕も何度か豆を買わせていただいてます。仙台行ってみたい。
[Fediverseでお世話になっている豆屋です｜まめや｜note](https://note.com/ncr/n/n852581e69a46)

今日の記事は、サーバーを閉じる時のお話です。
ActivityPub サーバーをやってきたけど、いろんな理由で閉じたい… と言うことがあると思います。
連合しているサーバーにできるだけ影響を与えず、かつ可能な限りお金をかけずにサーバーを閉じる方法を書きます。

僕は Mastodon サーバーしか運営していないので未確認ですが、他の ActivityPub 対応サーバーでも使える手だと思います。

## tl;dr

- Netlify を使って、無料で 410 Gone を返すヤツを作った
- tootctl self-destruct はどう動くか知らん

## 410 を返せとは言われるけど

サーバーを閉じたいと誰かに相談すれば、だいたい「しばらくの間は410 Goneを返した方がいい」と言われるわけですが、410 Goneを返すにもサーバーが必要なわけで、特に経済的な理由で閉じたいとき解決になってない、という話があります。

410 Gone をお金をかけずに返す方法があれば、そういう苦難から解放されるわけです。

それ、Netlify を使えば無料で出来ます。

## 無料で 410 Gone を返すヤツを作る

Netlify という静的なサイトを無料でホスティングするサービスがあります。
こいつを活用すると、閉鎖したサーバーに来たリクエストに対して 410 Goneを返すことができます。しかも**無料**です。
デプロイするだけでそれができるリポジトリを作りました。
[cyber-gene/netlify-410](https://github.com/cyber-gene/netlify-410)

## 使い方

1. リポジトリをフォークしましょう。
Netlify にデプロイするには、自分のリポジトリである必要があります。

1. Netlify のアカウントを持ってなければ作成する
有料プランもありますが、今回の用途であれば無料の STARTER で問題ないと思います。
[Netlify](https://www.netlify.com/)

1. サイトを作る
New site from Git を押す
![Netlify Overview](https://storage.googleapis.com/zenn-user-upload/dplv0jnj7mfwcsppo6mo54hkuop9)
GitHub 等のアカウントと連携させる画面が出てきます。Forkしたリポジトリがあるサービスを選択して連携させます。
![Netlify Step1](https://storage.googleapis.com/zenn-user-upload/le76safsuvli34xqfh7ihvlwhax6)
連携させると、リポジトリを選択するのが出てくるので、netlify-410 を選択
![Netlify Step2](https://storage.googleapis.com/zenn-user-upload/jcd7rd0xpoboq22xsvwf35ejicxm)
ビルド設定はそのままで動きます。Deploy siteを押すとサイトができます。
![Netlify Step3](https://storage.googleapis.com/zenn-user-upload/iaqlzclgqpfv7nrg14hxmz4qy28w)

1. ドメインの設定をする
Site setting → Domain management → Custom domains からドメインを追加します。
画面で指定されたDNSレコードを追加し、ドメインへのアクセスがNetlifyに向かうように設定します。
それをNetlify側が確認できたら、SSLの証明書とかを勝手に作ってくれます。らくちん！
※ 自分のドメインでアクセスできるようになるまでしばらく時間がかかります。気長に待ちましょう。
このへんが全部終わったら、Webブラウザからアクセスしてみましょう。
こんな画面が出ていればOKです！
![Netlify Step4](https://storage.googleapis.com/zenn-user-upload/upcqh7er87lyom3ti3x8grns3l7l)

## 本当に410返してんの？

Webブラウザの開発者ツールを使って、本当に410を返してるのか見てみましょう。ステータスが410になっていますね？
![Confirm return HTTP410](https://storage.googleapis.com/zenn-user-upload/9p4pxi9rdf5zpvau5z3l7ob56ndu)

他のサーバーから配信を受ける /inbox も見てみましょう。410なのでオッケー。
![Confirm return HTTO410](https://storage.googleapis.com/zenn-user-upload/s8vnl58q9gcn7mzojurxq8ulizh5)

これで、410を無料で連合しているサーバーに返すことができます。

## で、いつまで410返したらいいの？

ドメインの有効期限があるうちは、410返しとけばいいと思います。
ドメインを更新してまで410を返すべきとは、僕は思いません。無理の無い範囲でやっていけばいいと思います。

## おわりに
自分でサーバーを立てることは自由なので、閉じることも自由であるはずですが、他のサーバと密に連携して動いている関係上、サーバーを閉じると少なからず他のサーバーに影響を与えてしまいます。
が、この方法を使えば、少なくともドメインを維持している間は追加費用をかけずに、他のサーバーへの影響を最小限にして自分のサーバーを閉じることができます。
このへんは、いざサーバーを閉じるとなった時に二の足を踏む要因ではあるので、活用してみてください。

一度閉じてしまうと、同じドメインで ActivityPub のサーバーをもう一度立ち上げようとしても、うまく連合に参加できないことがあります。閉じる時はそのへんの覚悟を持ちましょう。

明日はオカどん管理人の kuloma1108 さんの記事です。