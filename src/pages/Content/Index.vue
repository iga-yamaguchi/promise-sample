<template>
    <v-layout>
        <div v-if="errorMessage" class="alert alert-danger" role="alert">{{ errorMessage }}</div>
        <v-card contextual-style="dark">
        <span slot="header">
        Promise Sample!
        </span>
            <div slot="body">
                <button @click="fetch" class="btn btn-primary">
                    <i v-if="nowLoading" class="fa fa-refresh  fa-spin"></i>
                    Fetch
                </button>
                <button @click="clear" class="btn btn-success">Clear</button>
            </div>
        </v-card>
        <v-card contextual-style="info">
            <span slot="header">
                Content
            </span>
            <div slot="body">
                <h1>{{ title }}</h1>
                <img :src="mainImageUrl" alt="">
                <p>{{ caption }}</p>
                <div class="row">
                    <v-card v-for="plan in plans" class="col-sm-2">
                        <span slot="header">{{ plan.title }}</span>
                        <div slot="body">
                            <p>{{ plan.room }}</p>
                            <p>{{ plan.price }}円</p>
                        </div>
                    </v-card>
                </div>
            </div>
        </v-card>
        <v-card>
            <span slot="header">Recomended</span>
            <div slot="body" class="row">
                <div v-for="contents in otherContents" class="card col-sm-3">
                    <img :src="contents.mainImageUrl" class="" alt="">
                    <div class="card-body">
                        <h3 class="card-title">{{ contents.title }}</h3>
                        <p class="card-text">{{ contents.caption }}</p>
                    </div>
                </div>
            </div>
        </v-card>
    </v-layout>
</template>

<script>
  /* ============
   * Home Index Page
   * ============
   *
   * The home index page.
   */

  import VLayout from '@/layouts/Default';
  import VCard from '@/components/Card';

  function get(url) {
    return new Promise((resolve, reject) => {
      const req = new XMLHttpRequest();
      req.open('GET', url);

      req.onload = () => {
        if (req.status === 200) {
          resolve(req.response);
        } else {
          reject(Error(req.statusText));
        }
      };

      req.onerror = () => {
        reject(Error('Network Error'));
      };

      req.send();
    });
  }

  function getJSON(url) {
    return get(url).then(JSON.parse);
  }

  // function getChapter(i) {
  //   storyPromise = storyPromise || getJSON('story.json');
  //
  //   return storyPromise.then(story => getJSON(story.chapterUrls[i]));
  // }


  export default {
    name: 'component-index',
    components: {
      VLayout,
      VCard,
    },
    data() {
      return {
        nowLoading: false,
        title: '',
        mainImageUrl: '',
        caption: '',
        plans: [],
        otherContents: [],
        errorMessage: '',
      };
    },
    methods: {
      fetch() {
        this.nowLoading = true;

        // 直列
        getJSON('/static/sample01.json')
          .then((contentResponse) => {
            const data = contentResponse.data;
            this.title = data.title;
            this.mainImageUrl = data.mainImageUrl;
            this.caption = data.caption;
          })
          .then(() => getJSON('/static/plan01.json')
            .then((planResponse) => {
              this.plans = planResponse.data;
            }))
          .then(() => getJSON('/static/otherContents01.json')
            .then((otherContentsResponse) => {
              this.otherContents = otherContentsResponse.data;
            }))
          .catch((error) => {
            this.errorMessage = error.message;
          })
          .then(() => {
            this.nowLoading = false;
          });
      },
      clear() {
        this.title = '';
        this.mainImageUrl = '';
        this.caption = '';
        this.plans = [];
        this.otherContents = [];
      },
    },
  };
</script>
<style>
    img {
        height: 200px;
    }
</style>
