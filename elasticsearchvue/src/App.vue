<template>
  <h1>Search By Movie Name</h1>
  <input v-model="movie" />
  <button @click="searchMovie">Search</button>
  <div v-if="results.length > 0">
    <div
      v-for="result in results"
      :key="result['_source'].id"
      class="eachMovie"
    >
      <img :src="result._source.poster" class="eachMoviePoster" />
      <div class="eachMovieText">
        <div class="eachMovieName">{{ result["_source"].title }}</div>
        <p class="eachMovieOverview">{{ result["_source"].overview }}</p>
      </div>
    </div>
  </div>
  <div v-if="results.length === 0 && touchInput != 0">
    Sorry we can't find the movie
  </div>
</template>

<script>
const elasticsearch = require("elasticsearch");
const client = new elasticsearch.Client({
  host: "http://localhost:9200",
});
export default {
  name: "App",
  data() {
    return {
      movie: "",
      results: [],
      touchInput: 0,
    };
  },
  methods: {
    searchMovie() {
      this.touchInput = 1;
      client
        .search({
          index: "movies",
          type: "_doc",
          body: {
            query: {
              match: {
                title: this.movie,
              },
            },
          },
        })
        .then((resp) => {
          this.results = resp.hits.hits;
          console.log(resp.hits.hits);
          return resp.hits.hits;
        });
    },
  },
};
</script>

<style scoped>
.eachMovie {
  width: 100%;
  height: 200px;
  display: flex;
  margin: 20px 10px;
}

.eachMoviePoster {
  height: 100%;
  width: auto;
}

.eachMovieText {
  padding: 5px 20px;
}

.eachMovieName {
  font-size: 24px;
}

.eachMovieOverview {
}
</style>
