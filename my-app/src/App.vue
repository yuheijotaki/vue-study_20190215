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

<style lang="scss" scoped>
html,* {
  margin: 0;
  padding: 0
}
#app {
  font-family: Helvetica Neue, Helvetica, Arial, 'Hiragino Kaku Gothic ProN', Meiryo, sans-serif;
  -webkit-text-size-adjust: 100%;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-rendering: optimizeLegibility;
  font-feature-settings : "palt";
  max-width: 1200px;
  margin: 40px auto;
  main {
    margin-top: 40px;
    ul {
      list-style: none;
      display: flex;
      flex-wrap: wrap;
      li {
        font-size: 14px;
        line-height: 1.2;
        width: 22%;
        margin-top: 4%;
        margin-right: 4%;
        &:nth-child(-n+4) {
          margin-top: 0;
        }
        &:nth-child(4n) {
          margin-right: 0;
        }
        a {
          display: block;
          text-decoration: none;
          position: relative;
          figure {
            font-size: 0;
            line-height: 0;
            img {
              width: 100%;
              height: auto;
            }
          }
          .wrap {
            display: none;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            .inner {
              position: absolute;
              top: 50%;
              left: 50%;
              transform: translate(-50%, -50%);
              width: 100%;
              padding: 0 20px;
              box-sizing: border-box;
              text-align: center;
              letter-spacing: 0.025em;
              h2 {
                font-size: 16px;
                line-height: 1.2;
              }
              p {
                margin-top: .5em;
                font-size: 12px;
                line-height: 1.4;
              }
            }
          }
          &:hover {
            .wrap {
              display: block;
            }
          }
        }
      }
    }
  }
}
</style>
