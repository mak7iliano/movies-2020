<template>
  <div id="app">
    <header class="header">
      <div class="mainer">
        <div class="header-movie-info">
          <div class="caption">RINGS</div>
          <div class="sub-text">EVIL IS REBORN</div>
          <div
            class="description"
          >Paramount Pictures have just released the first trailer for the upcoming Horror movie Rings.</div>
        </div>
      </div>
    </header>

    <div class="mainer">
      <ul class="block-categories">
        <li
          v-for="genre in genresList"
          :key="genre.id"
          :class="{active: genre.id === gengeActiveId}"
          @click="changeGenre(genre.id)"
        >{{genre.name}}</li>
      </ul>

      <ul class="block-movies">
        <li v-for="movie in moviesList" :key="movie.id">
          <div class="card">
            <div class="label" v-if="movie.popularity > 70">popular</div>
            <div class="image">
              <img v-if="movie.poster_path" :src="imageUrl + movie.poster_path" alt />
              <img v-else src="./assets/no-poster.jpg" alt />
              <div class="backdrop"></div>
            </div>
            <div class="wrapper">
              <div class="name">{{movie.title}}</div>
              <div class="category">{{getGenreNames(movie.genre_ids)}}</div>
              <div class="rating-wrapper">
                <div class="block-rating">
                  <span :style="{width: movie.vote_average*10 + '%'}"></span>
                </div>
                <div class="value">{{movie.vote_average}}</div>
              </div>
            </div>
          </div>
        </li>
        <li class="empty"></li>
        <li class="empty"></li>
      </ul>

      <div class="block-stepper">
        <button class="show-more-btn" @click="moreMovies()">
          <em></em>
          <em></em>
          <em></em>
          <span v-text="currentPage" v-if="currentPage > 1"></span>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      apiUrl: "https://api.themoviedb.org/",
      imageUrl: "https://image.tmdb.org/t/p/w500/",
      apiKey: "796e965a803b8b858c92c3301679502c",
      lang: "ru-RU",
      genresList: [],
      genresListOriginal: [],
      gengeActiveId: null,
      moviesList: [],
      currentPage: 1
    };
  },
  methods: {
    getGenres() {
      fetch(
        this.apiUrl +
          "3/genre/movie/list?api_key=" +
          this.apiKey +
          "&language=" +
          this.lang
      )
        .then(result => {
          return result.json();
        })
        .then(result => {
          this.genresList = result.genres.slice(0, 6);
          this.genresListOriginal = result.genres;
          this.gengeActiveId = this.genresList[0].id;

          this.getMovies(this.gengeActiveId);
        });
    },
    getMovies(genreId) {
      fetch(
        this.apiUrl +
          "3/discover/movie?api_key=" +
          this.apiKey +
          "&language=" +
          this.lang +
          "&sort_by=popularity.desc&include_adult=false&include_video=false&page=" +
          this.currentPage +
          "&with_genres=" +
          genreId
      )
        .then(result => {
          return result.json();
        })
        .then(result => {
          this.moviesList = this.moviesList.concat(result.results);
        });
    },
    moreMovies() {
      this.currentPage++;
      this.getMovies(this.gengeActiveId);
    },
    getGenreNames(ids) {
      let result = ``;
      for (let id of ids) {
        const index = this.genresListOriginal.findIndex(
          genre => genre.id === id
        );
        result += this.genresListOriginal[index].name + ", ";
      }
      return result.slice(0, -2);
    },
    changeGenre(id) {
      this.gengeActiveId = id;
      this.moviesList = [];
      this.currentPage = 1;
      this.getMovies(id);
    }
  },
  created: function() {
    this.getGenres();
  }
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@200;400;500;600;700&display=swap");
@import "./scss/styles.scss";
</style>
