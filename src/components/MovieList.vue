<script setup lang="ts">
const movies = [
  {
    id: 1,
    title: 'Interstellar',
    genre: 'Science-Fiction',
    releaseYear: 2014,
    rating: 5,
    watched: true,
  },
  {
    id: 2,
    title: 'Inception',
    genre: 'Science-Fiction',
    releaseYear: 2010,
    rating: 4,
    watched: true,
  },
  {
    id: 3,
    title: 'Avatar',
    genre: 'Fantasy',
    releaseYear: 2009,
    rating: null,
    watched: false,
  },
]
</script>

<template>
  <div class="watchlist">
    <h1>🎬 Meine MovieWatchlist</h1>

    <p class="subtitle">Deine persönliche Filmsammlung</p>

    <h2>Meine Filme</h2>

    <ul>
      <li v-for="movie in movies" :key="movie.id">
        <span class="movie-icon">🎞️</span>

        <strong>{{ movie.title }}</strong>

        <p>{{ movie.genre }} · {{ movie.releaseYear }}</p>

        <p>
          ⭐ Bewertung:
          {{ movie.rating !== null ? movie.rating + '/5' : 'Noch nicht bewertet' }}
        </p>

        <span :class="movie.watched ? 'watched' : 'pending'">
          {{ movie.watched ? '✓ Gesehen' : '○ Noch nicht gesehen' }}
        </span>
      </li>
    </ul>
  </div>
</template>
<style scoped>
:global(body) {
  background-color: #141414;
  color: white;
  font-family: Arial, sans-serif;
  margin: 0;
  display: block;
}

:global(#app) {
  display: block;
  max-width: none;
  margin: 0;
  padding: 0;
}

.watchlist {
  min-height: 100vh;
  padding: 50px 8%;
  background: linear-gradient(135deg, #1c1c1c, #080808);
}

h1 {
  color: #e50914;
  font-size: 42px;
  margin-bottom: 10px;
}

.subtitle {
  color: #b3b3b3;
  font-size: 18px;
  margin-bottom: 40px;
}

h2 {
  color: white;
  font-size: 26px;
}
ul {
  list-style: none;
  padding: 0;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 25px;
  margin-top: 30px;
}

li {
  background-color: #242424;
  padding: 30px;
  border-radius: 12px;
  border: 1px solid #333;
  transition: 0.3s;
}

li:hover {
  transform: scale(1.04);
  border-color: #e50914;
}

.movie-icon {
  display: block;
  font-size: 45px;
  margin-bottom: 20px;
}

strong {
  display: block;
  font-size: 24px;
  margin-bottom: 15px;
}

li p {
  color: #b3b3b3;
  margin-bottom: 15px;
}

.watched {
  display: inline-block;
  background-color: #176b39;
  padding: 8px 14px;
  border-radius: 20px;
  font-size: 14px;
}

.pending {
  display: inline-block;
  background-color: #9b1c24;
  padding: 8px 14px;
  border-radius: 20px;
  font-size: 14px;
}
</style>