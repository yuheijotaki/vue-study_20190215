<template>
  <div id="app">
    <header>
      <h1>works.yuheijotaki.com</h1>
    </header>
    <main>
      <ul v-for="post in posts" :key="post.title.rendered">
        <li>
          <p>title: <a v-bind:href="post.link" target="_blank">{{post.title.rendered}}</a></p>
          <pre>
          categories: {{post.categories}}
          post_color_bg: {{post.acf.post_color_bg}}
          post_color_letter: {{post.acf.post_color_letter}}
          post_custom_title: {{post.acf.post_custom_title}}
          post_url: {{post.acf.post_url}}
          post_archive: {{post.acf.post_archive}}
          post_thumbnail: {{post.acf.post_thumbnail}}
          </pre>
        </li>
        <hr>
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
      })
      .catch( error => {
        console.log(error);
      });
    }
  }
};
</script>

<style lang="scss" scoped>
html,* {
  margin: 0;
  padding: 0
}
#app {
  max-width: 800px;
  margin: 40px auto;
  main {
    margin-top: 20px;
    .post_list {
      padding-left: 1.4em;
      li {
        margin-bottom: 5px;
        font-size: 14px;
        line-height: 1.2;
        &:last-child {
          margin-bottom: 0;
        }
        a {
        }
        pre {
          font-size: 10px;
        }
      }
    }
  }
}
</style>
