<template>
  <div class="big_box">
    <h3>π {{ this.$store.state.username }}λμ΄ μ°ν μν π</h3>
  <div>
    <div class="overflow-auto">
      <!-- μ°ν μνκ° μμλ, νλ©΄ -->
      <ul class="box" v-if="flag">
        <li v-for="perPageMovie in perPageMovies" v-bind:key="perPageMovie.id">
          <AllMovieCard class="card" v-bind:posterPath="perPageMovie.poster_path" 
            v-bind:movieId="perPageMovie.id"
          />
        </li>
      </ul>
      <!-- μ°ν μνκ° μμλ, νλ©΄ -->
      <p v-else>μ°ν μνκ° μμ΅λλ€.<br> κ΄μ¬μλ μνμ μ’μμλ₯Ό λλ¬μ£ΌμΈμ π</p>

      <!-- μ°ν μνκ° μμλ, νλ©΄-νμ΄μ§λ€μ΄μ -->
        <b-pagination v-if="flag" class="pagination"
        v-model="currentPage"
        :total-rows="rows"
        :per-page="perPage"
      ></b-pagination>
    </div>
  </div>
  </div>
</template>

<script>
import axios from "axios";
import AllMovieCard from '@/components/AllMovieCard';

export default {
  name: "WishList",
  components: { 
    AllMovieCard,
  },
     data(){
    return{
      flag: false,
      perPage:14,
      currentPage:1,
      movies: [],
    };
  },
  created(){
    this.getWishList()
    this.$store.dispatch('saveUserInfo', this.$store.state.token)
    this.isFlag()
  },
  computed:{
    perPageMovies() {
      const newMovies = this.movies.slice(
        this.perPage * this.currentPage - this.perPage,
        this.perPage * this.currentPage
      );
      return newMovies;
    },
    rows() {
      return this.movies.length;
    },
  },
  methods:{
    getWishList() {
      const URL = 'http://127.0.0.1:8000'
      axios({
        method: 'get',
        url: `${URL}/api/v1/movies/`,
      })
        .then((res) => {
          // μ μ κ° μ’μμν μνλ₯Ό νμνλ€.
          const mymovies = res.data.filter(movie => movie.like_users.includes(this.$store.state.user_pk));
          this.movies = mymovies
        })
        .catch((err) => {
          console.log(err)
        })
    },
    isFlag() {
      const URL = 'http://127.0.0.1:8000'
      axios({
        method: 'get',
        url: `${URL}/api/v1/movies/`,
      })
        .then((res) => {
          // μ μ κ° μ’μμν μνλ₯Ό νμνλ€.
          const mymovies = res.data.filter(movie => movie.like_users.includes(this.$store.state.user_pk));
          if(mymovies.length !== 0){
            this.flag = true
          }
        })
        .catch((err) => {
          console.log(err)
        })
    },
  }
}
</script>

<style scoped>
h3{margin-top:50px;}

.big_box{
  background-color: #ECEEFF;
  text-align: center;
}
p{
  margin-top:50px;
  margin-bottom: 70px;
}
.card {
  margin-bottom: 50px;
  padding:20px;
  border:0;
  background-color: #ECEEFF;
}
.box {
  display: grid;
  margin-top: 50px;
  width:100%;
  grid-template-columns: repeat(7, 1fr);
}
.pagination{
  display: flex;
  justify-content: center;
}
::v-deep .pagination  .page-link {
      background: rgb(221, 224, 255) ;
      color: black;
    }
::v-deep .pagination .active button{
      background: #a89be2;
      color: white;
      border: white;
    }

</style>