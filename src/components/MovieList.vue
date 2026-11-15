<script setup lang="ts">
import { ref, onMounted } from 'vue'

type Movie = {
  id: number
  title: string
  genre: string
  releaseYear: number
  rating: number | null
  watched: boolean
}

const apiUrl = 'https://movie-watchlist-backend-lq4w.onrender.com/movies'

const movies = ref<Movie[]>([])

const newMovie = ref<Omit<Movie, 'id'>>({
  title: '',
  genre: '',
  releaseYear: new Date().getFullYear(),
  rating: null,
  watched: false,
})

const errorMessage = ref('')
const saving = ref(false)
const loading = ref(true)

async function loadMovies() {
  loading.value = true
  errorMessage.value = ''

  try {
    const response = await fetch(apiUrl)

    if (!response.ok) {
      throw new Error('Filme konnten nicht geladen werden')
    }

    movies.value = await response.json()
  } catch (error) {
    errorMessage.value = 'Filme konnten nicht geladen werden.'
    console.error(error)
  } finally {
    loading.value = false
  }
}

async function addMovie() {
  if (saving.value || loading.value) return

  errorMessage.value = ''

  if (!newMovie.value.title.trim() || !newMovie.value.genre.trim()) {
    errorMessage.value = 'Bitte gib einen Filmtitel und ein Genre ein.'
    return
  }

  saving.value = true

  try {
    const response = await fetch(apiUrl, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(newMovie.value),
    })

    if (!response.ok) {
      throw new Error('Film konnte nicht gespeichert werden')
    }

    const savedMovie: Movie = await response.json()
    movies.value.push(savedMovie)

    newMovie.value = {
      title: '',
      genre: '',
      releaseYear: new Date().getFullYear(),
      rating: null,
      watched: false,
    }
  } catch (error) {
    errorMessage.value = 'Film konnte nicht gespeichert werden.'
    console.error(error)
  } finally {
    saving.value = false
  }
}

onMounted(loadMovies)
</script>

<template>
  <div class="watchlist">
    <h1>🎬 Meine MovieWatchlist</h1>

    <p class="subtitle">Deine persönliche Filmsammlung</p>

    <form class="movie-form" @submit.prevent="addMovie">
      <h2>Neuen Film hinzufügen</h2>

      <label for="title">Filmtitel</label>
      <input
        id="title"
        v-model.trim="newMovie.title"
        type="text"
        placeholder="z. B. Inception"
        required
      />

      <label for="genre">Genre</label>
      <input
        id="genre"
        v-model.trim="newMovie.genre"
        type="text"
        placeholder="z. B. Science-Fiction"
        required
      />

      <label for="releaseYear">Erscheinungsjahr</label>
      <input
        id="releaseYear"
        v-model.number="newMovie.releaseYear"
        type="number"
        min="1888"
        required
      />

      <label for="rating">Bewertung</label>
      <select id="rating" v-model="newMovie.rating">
        <option :value="null">Noch nicht bewertet</option>
        <option :value="1">1/5</option>
        <option :value="2">2/5</option>
        <option :value="3">3/5</option>
        <option :value="4">4/5</option>
        <option :value="5">5/5</option>
      </select>

      <label class="checkbox">
        <input v-model="newMovie.watched" type="checkbox" />
        Bereits gesehen
      </label>

      <button type="submit" :disabled="saving || loading">
        {{ saving ? 'Wird gespeichert...' : '+ Film hinzufügen' }}
      </button>
    </form>

    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>

    <h2>Meine Filme</h2>

    <p v-if="loading" class="info">Filme werden geladen...</p>

    <p v-else-if="movies.length === 0 && !errorMessage" class="info">
      Du hast noch keine Filme gespeichert.
    </p>

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

.movie-form {
  display: flex;
  flex-direction: column;
  gap: 12px;
  background-color: #242424;
  padding: 30px;
  border-radius: 12px;
  border: 1px solid #333;
  max-width: 600px;
  margin-bottom: 40px;
}

.movie-form h2 {
  margin-bottom: 15px;
}

.movie-form label {
  color: #ddd;
  font-size: 15px;
}

.movie-form input,
.movie-form select {
  background-color: #161616;
  color: white;
  border: 1px solid #444;
  border-radius: 6px;
  padding: 12px;
  font-size: 16px;
  width: 100%;
  box-sizing: border-box;
}

.movie-form input:focus,
.movie-form select:focus {
  outline: none;
  border-color: #e50914;
}

.checkbox {
  display: flex;
  align-items: center;
  gap: 10px;
  margin: 10px 0;
}

.checkbox input {
  width: auto;
  accent-color: #e50914;
}

button {
  background-color: #e50914;
  color: white;
  border: none;
  padding: 14px;
  border-radius: 6px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  transition: 0.3s;
}

button:hover {
  background-color: #b80710;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.error {
  color: #ff6b6b;
  margin-bottom: 20px;
}

.info {
  color: #b3b3b3;
  margin-top: 20px;
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
