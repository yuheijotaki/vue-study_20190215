# Vue.js / JSON から情報を引っ張ってくる その 5

## やること

- [ポートフォリオページ](https://works.yuheijotaki.com) のコンテンツのエンドポイントを WP REST API を用いて作成
- 記事タイトルやサムネイル、ACF で登録している値を Vue.js で描画

### WordPress の REST API で ACF を使う

[ACF to REST API \| WordPress\.org](https://ja.wordpress.org/plugins/acf-to-rest-api/)

を入れると、

`https://works.yuheijotaki.com/wp-json/wp/v2/posts` で出力される JSON に

```json
"acf": {
  "post_color_bg":"#ffffff",
  "post_color_letter":"#000000",
  "post_custom_title":"",
  "post_url":"https:\/\/tatsuhikoniijima.com\/",
  "post_archive":false,"post_thumbnail":173
}
```

のように `acf` のオブジェクトが入る。

### 参考にした URL など

- [WordPress REST API で投稿の取得から新規投稿を行う ｜ Tips Note by TAM](https://www.tam-tam.co.jp/tipsnote/cms/post10274.html)
- [API \- WP REST API（axios で読み込み）でページ読み込みと同時にレンダリングする｜ teratail](https://teratail.com/questions/110810)
