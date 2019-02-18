# Vue.js / JSON から情報を引っ張ってくる その5

## やること

- [ポートフォリオページ](https://works.yuheijotaki.com) のコンテンツのエンドポイントを WP REST API を用いて作成
- 記事タイトルやサムネイル、ACF で登録している値を Vue.js で描画

### 画面読み込み時に `methods` のfunctionをレンダリングする

```javascript
...
created: function(){
  this.request();
},
methods: {
  request: function(){
  ...
  }
},
...
```

参考

- [API \- WP REST API（axios で読み込み）でページ読み込みと同時にレンダリングする｜ teratail](https://teratail.com/questions/110810)

- [WordPress REST API で投稿の取得から新規投稿を行う ｜ Tips Note by TAM](https://www.tam-tam.co.jp/tipsnote/cms/post10274.html)

### REST API で Advanced Custom Fields を使う

[WP REST API](https://ja.wp-api.org/) に加えて、[ACF to REST API \| WordPress\.org](https://ja.wordpress.org/plugins/acf-to-rest-api/) を入れると、`https://works.yuheijotaki.com/wp-json/wp/v2/posts` で出力される JSON に下記のように登録されている各 `acf` のオブジェクトが入る。

```json
"acf": {
  "post_color_bg":"#ffffff",
  "post_color_letter":"#000000",
  "post_custom_title":"",
  "post_url":"https:\/\/tatsuhikoniijima.com\/",
  "post_archive":false,
  "post_thumbnail":173
}
```

Vue.js 側では、

`{post.acf.post_url}` のようにする。

### REST API で投稿ごとのカテゴリーを取得する

投稿ごとのカテゴリー名を取得するにはWordPressのテーマ側（functions.php）を編集する必要がありそう。

functions.php

```php
// 個別投稿毎にカテゴリ名を取得する
add_action( 'rest_api_init', 'register_category_name' );
function register_category_name() {
	//register_rest_field関数を用いget_category_name関数からカテゴリ名を取得し、追加する
	register_rest_field( 'post',
		'category_name',
		array(
			'get_callback' => 'get_category_name'
		)
	);
}

// $objectは現在の投稿の詳細データが入る
function get_category_name( $object ) {
	$formatted_categories = array();
	$categories = get_the_category( $object['id'] );
	foreach ($categories as $category) {
		$formatted_categories[] = $category->name;
	}
	$formatted_categories = implode(' ,', $formatted_categories); // カテゴリー配列をカンマ区切りに変換
	return $formatted_categories;
}
```

これで JSON には

```json
"category_name": "Front-end,WordPress"
```

のようにカテゴリー名が出力される。  
本当は App.vue 側でカンマ区切りにしたほうが良さげでしたが詰まって見送りにしました。。

Vue.js 側では、

`{{post.category_name}}` のようにする。

参考

- [WP\-REST APIで個別投稿毎にカテゴリ名を取得する \- Qiita](https://qiita.com/yumayamada1029/items/c40e40200899330f957b)
- [custom post types \- Get the Category Name instead of ID from WP\-API \- WordPress Development Stack Exchange](https://wordpress.stackexchange.com/questions/287931/get-the-category-name-instead-of-id-from-wp-api)

### REST API で画像URLを取得する

もともとACFで画像ID出力にしているフィールドの画像URLを出力させるのも functions.php に追記

functions.php

```php
function ws_register_images_field() {
	register_rest_field(
		'post',
		'images',
		array(
			'get_callback'    => 'ws_get_images_urls',
			'update_callback' => null,
			'schema'          => null,
		)
	);
}
add_action( 'rest_api_init', 'ws_register_images_field' );

function ws_get_images_urls( $object, $field_name, $request ) {
	$full = wp_get_attachment_image_src( get_field( 'post_thumbnail', $object->id ), 'full' );
	$full_url = $full['0'];
	return array(
		'full'  => $full_url,
	);
}
```

これで JSON には、

```json
...
"images":{
	"full":"https:\/\/works.yuheijotaki.com\/wwyjp\/wp-content\/uploads\/2018\/09\/tatsuhikoniijima.jpg"
	}
...
```

のように画像URLが出力される。

Vue.js 側では、

`<img v-bind:src="post.images.full">` のようにする。

参考

- [json \- Get Image URL instead of Attachment Id in Rest API \- WordPress Development Stack Exchange](https://wordpress.stackexchange.com/questions/271448/get-image-url-instead-of-attachment-id-in-rest-api)

## App.vue

一部省略

```javascript
<template>
  <div id="app">
    <header>
      <h1>works.yuheijotaki.com</h1>
    </header>
    <main>
      <ul>
        <li v-for="(post,index) in posts" :key="index">
          <a v-bind:href="post.acf.post_url" target="_blank">
            <figure><img v-bind:src="post.images.full" v-bind:alt="post.title.rendered"></figure>
            <div class="wrap" v-bind:style="{ color: post.acf.post_color_letter, background: post.acf.post_color_bg }">
              <div class="inner">
                <h2>{{post.title.rendered}}</h2>
                <p>{{post.category_name}}</p>
              </div>
            </div>
          </a>
        </li>
      </ul>
    </main>
  </div>
</template>

<script>
// normalize.css を読み込む
import "normalize.css";
// Ajax通信ライブラリ
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      posts: []
    }
  },
  created: function(){
    this.request();
  },
  methods: {
    request: function(){
      axios.get( 'https://works.yuheijotaki.com/wp-json/wp/v2/posts?per_page=100' )
      .then( response => {
        this.posts = response.data;
        // console.log(this.posts);
      })
      .catch( error => {
        console.log(error);
      });
    }
  }
};
</script>
```

## まとめ

**[GitHub](https://github.com/yuheijotaki/vue-study_20190215)**

- 投稿の情報引っ張ってくるのだけなので前回よりはつまづかずにできた。
- カテゴリーの JSON 格納形式など REST APIの出力を自分でいじれるからいいのですが、Vue.js 側で変換などしてあげる処理はいちいちつまづく。。JavaScript の問題なのか、Vue.js の問題なのかを区別して対処が必要と思う。
- 次は元サイトと同じようにカテゴリーのフィルターをつくる。