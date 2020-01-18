<template>
  <div id="app">
    <div class="wrap">
      <div class="search">
        <input
          type="text"
          class="searchTerm"
          placeholder="What are you looking for?"
          v-model="keyword"
        />
        <button type="submit" class="searchButton" v-on:click="search()">Search</button>
      </div>
    </div>
    <pagination
      :current="currentPage"
      :total="totalPhotos"
      :perPage="perPage"
      @page-changed="getData"
    ></pagination>

    <section class="grid" v-if="photos.length > 1">
      <div class="grid__item card" v-for="photo in photos" :key="photo.id">
        <div class="card__body">
          <img :src="photo.urls.small" alt="img" />
        </div>
        <div class="card__footer media">
          <img :src="photo.user.profile_image.small" alt="user" class="media__obj" />
          <div class="media__body">
            <a :href="photo.user.portfolio_url" target="_blank">{{photo.user.name}}</a>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import Pagination from "./components/Pagination.vue";
import VueResource from "vue-resource";

let appID = "ec820a98f428e4c40b773e4c8935d285f3fa8d6dd09c5a6145f1066a15be1aab";

export default {
  name: "app",
  data() {
    return {
      photos: [],
      totalPhotos: 0,
      perPage: 9,
      currentPage: 1,
      keyword: "",
      searchMode: false
    };
  },
  components: {
    pagination: Pagination
  },
  methods: {
    fetchPhotos(page) {
      let options = {
        params: {
          client_id: appID,
          page: page,
          per_page: this.perPage
        }
      };
      this.$http
        .get("https://api.unsplash.com/photos", options)
        .then(res => {
          console.log(res);
          this.photos = res.data;
          this.totalPhotos = parseInt(res.headers.get("x-total"));
          this.currentPage = page;
        })
        .catch(err => console.log(err));
    },
    search(page) {
      this.searchMode = true;
      let options = {
        params: {
          client_id: appID,
          page: page,
          per_page: this.perPage,
          query: this.keyword
        }
      };
      this.$http
        .get("https://api.unsplash.com/search/photos", options)
        .then(res => {
          console.log(res);
          let azz = res.data.results;
          this.photos = azz;

          this.totalPhotos = parseInt(res.headers.get("x-total"));
          this.currentPage = page;
        })
        .catch(err => console.log(err));
    },
    getData(page) {
      if (this.searchMode) {
        this.search(page);
      } else {
        this.fetchPhotos(page);
      }
    }
  },
  created() {
    this.getData(this.currentPage);
  }
};
</script>

<style>
* {
  box-sizing: border-box;
}

body {
  background-color: #f5f6f7;
  display: flex;
  justify-content: center;
  padding: 80px 0;
}

#app {
  width: 100%;
}

.grid {
  width: 100%;
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  margin: 0 auto;
}

.grid__item {
  width: 30%;
  flex-grow: 1;
  flex-shrink: 1;
  margin: 0 20px 40px;
}

.card {
  background-color: #fff;
  overflow: hidden;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
  border-radius: 2px;
  line-height: 0;
  cursor: pointer;
}

.card:hover {
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.2);
}

.card__body {
  width: 100%;
  height: 215px;
  overflow: hidden;
  background-color: #eee;
}

.card__body img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.card__footer {
  width: 100%;
  padding: 10px 15px;
}

.media__obj {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background-color: #d8d8d8;
  margin-right: 15px;
  float: left;
}

.media__body {
  width: 100%;
  height: 32px;
  line-height: 32px;
}

.media__body a {
  font-size: 15px;
  color: #999;
}

.media__body a:hover {
  text-decoration: none;
}
@import url(https://fonts.googleapis.com/css?family=Open+Sans);

body {
  background: #f2f2f2;
  font-family: "Open Sans", sans-serif;
}

.search {
  width: 100%;
  position: relative;
  display: flex;
}

.searchTerm {
  width: 100%;
  border: 3px solid #00b4cc;
  border-right: none;
  padding: 5px;
  height: 36px;
  border-radius: 5px 0 0 5px;
  outline: none;
  color: #9dbfaf;
}

.searchTerm:focus {
  color: #00b4cc;
}

.searchButton {
  width: auto;
  height: 36px;
  border: 1px solid #00b4cc;
  background: #00b4cc;
  text-align: center;
  color: #fff;
  border-radius: 0 5px 5px 0;
  cursor: pointer;
  font-size: 14px;
}

/*Resize the wrap to see the search bar change!*/
.wrap {
  width: 30%;
  position: absolute;
  top: 10%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding-bottom: 20px;
}
</style>
