<script setup>
import { ref, computed, onMounted } from 'vue'
const query = ref('')
const my_anime = ref([])
const search_results = ref([])
const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) => {
    return a.title.localeCompare(b.title)
  })
})
const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
  fetch(url).then(res => res.json()).then(res => {
    console.log(res.data)
    search_results.value = res.data
  })
}
const handleInput = e => {
  if (!e.target.value) {
    search_results.value = []
  }
}
const addAnime = anime => {
  search_results.value = []
  query.value = ''
  my_anime.value.push({
    id: anime.id,
    title: anime.title,
    image: anime.images.jpg.image_url,
    total_episodes: anime.episodes,
    watched_episodes: 0
  })
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}
const increaseWatch = anime => {
  anime.watched_episodes++
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}
const decreaseWatch = anime => {
  anime.watched_episodes--
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}
onMounted(() => {
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
})
</script>


<template>
  <div class="container is-max-desktop">

    <div class="header">
      <h1 class="title">Animes Tracker</h1>
      <div class="field is-grouped">
        <p class="control is-expanded">
          <input class="input" type="text" placeholder="search for an anime..." v-model="query" @input="handleInput">
        </p>
        <p class="control">
          <a class="button is-danger" @click="searchAnime">
            Search
          </a>
        </p>
      </div>
    </div>

    <div class="searched-results" v-if="search_results.length > 0">
      <h2 class="subtitle">Results</h2>
      <div class="box" v-for="anime in search_results">
        <article class="media">
          <div class="media-left">
            <figure class="image is-64x64">
              <img :src="anime.images.jpg.image_url" alt="Image">
            </figure>
          </div>
          <div class="media-content">
            <div class="content">
              <p>
                <strong>{{anime.title}}</strong>
                <br>
              <p :title="anime.synopsis" v-if="anime.synopsis">
                {{anime.synopsis.slice(0,200)}}...
              </p>
              </p>
            </div>
            <div class="add-wishlist">
              <btn class="button is-danger is-light" @click="addAnime(anime)">
                <span class="icon is-small">
                  <i class="fas fa-heart" aria-hidden="true"></i>
                </span>
              </btn>
            </div>
          </div>
        </article>
      </div>
    </div>

    <div class="wishlisted">
      <h2 class="subtitle">My wishlist</h2>
      <p v-if="my_anime.length === 0">Your wishlist is empty, search for an anime and add it pressing the heart
        button.
      </p>
      <div class="card-container">
        <div class="card wishlist-card" v-for="anime in my_anime_asc">
          <div class="card-content">
            <div class="media">
              <div class="media-left">
                <figure class="image is-48x48">
                  <img :src="anime.image" alt="Placeholder image">
                </figure>
              </div>
              <div class="media-content">
                <p class="title is-4">{{anime.title}}</p>
                <p class="subtitle is-6">Watched episodes: {{anime.watched_episodes}} / {{anime.total_episodes}}</p>
              </div>
            </div>
          </div>
          <div>
            <div class="wishlist-buttons">
              <btn class="button is-danger is-light" @click="decreaseWatch(anime)" v-if="anime.watched_episodes > 0">
                <span class="icon is-small">
                  <i class="fas fa-minus" aria-hidden="true"></i>
                </span>
              </btn>
              <btn class="button is-danger is-light" @click="increaseWatch(anime)"
                v-if="anime.total_episodes !== anime.watched_episodes">
                <span class="icon is-small">
                  <i class="fas fa-plus" aria-hidden="true"></i>
                </span>
              </btn>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
  <footer class="footer">
    <div class="content has-text-centered">
      <p>
        <strong>Anime tracker</strong> by <a href="https://salhi-walid.netlify.app/">Walid Salhi Belkacem</a>. This app
        is builded with <a href="https://vuejs.org/">Vue.js</a> & <a href="https://bulma.io/">Bulma</a>.
        <br>
        © 2022
      </p>
    </div>
  </footer>

</template>

<style scoped>
.header {
  margin: 40px 200px;
}

.searched-results {
  margin: 40px 0px;
}

.wishlisted {
  margin: 40px 0px;
}

.add-wishlist {
  display: flex;
  justify-content: flex-end;
}

.wishlist-card {
  width: 310px;
}

.card-container {
  display: flex;
  flex-wrap: wrap;
  align-items: flex-start;
  gap: 10px;
}

.wishlist-buttons {
  display: flex;
  justify-content: end;
  gap: 10px;
  margin: 10px;
}
</style>
