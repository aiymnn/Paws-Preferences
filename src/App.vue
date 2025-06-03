<script setup lang="ts">
import { ref, onMounted } from "vue";

const totalCats = 10;
const currentIndex = ref(0);
const isLoading = ref(true);

//define cat model properties have
interface Cat {
  id: string;
  url: string;
}

const cats = ref<Cat[]>([]);
const catsLike = ref<Cat[]>([]);
const catsDislike = ref<Cat[]>([]);

const fetchRandomCats = async () => {
  isLoading.value = true;
  // define fetchedCat followed Cat properties
  const fetchedCats: Cat[] = [];

  for (let i = 0; i < totalCats; i++) {
    //get random id of cat
    const id = Math.random().toString(36).substring(7);
    // get images of cat
    const url = `https://cataas.com/cat?width=400&height=300&timestamp=${Date.now()}-${i}`;
    // console.log(url);
    // add into fetchedCats
    fetchedCats.push({ id, url });
  }

  // console.log(fetchedCats);

  // add item from fetchedCats into cats
  cats.value = fetchedCats;

  isLoading.value = false;
};

onMounted(fetchRandomCats);

const likeCat = () => {
  // add cat's index into catsLike
  catsLike.value.push(cats.value[currentIndex.value]);
  currentIndex.value++;
};

const dislikeCat = () => {
  // add cat's index into catsDislike
  catsDislike.value.push(cats.value[currentIndex.value]);
  currentIndex.value++;
};

const isDone = () => currentIndex.value >= cats.value.length;

// Swipe gesture detect
let startX = 0;
const onTouchStart = (e: TouchEvent) => {
  startX = e.touches[0].clientX;
};
const onTouchEnd = (e: TouchEvent) => {
  const diff = e.changedTouches[0].clientX - startX;
  // swipe left to right = dislike
  if (diff > 50) dislikeCat();
  //swap right to left = like
  else if (diff < -50) likeCat();
};
</script>

<template>
  <div class="container">
    <h1 class="title">🐾 Paws & Preferences</h1>
    <div v-if="!isDone()" class="swipe-hint">
      <span>⬅️ Swipe left to dislike</span>
      <span>Swipe right to like ➡️</span>
    </div>

    <div v-if="isLoading" class="loading">Loading cute cats...</div>

    <div v-else-if="!isDone()" class="card-wrapper">
      <transition name="fade" mode="out-in">
        <img
          class="cat-img"
          :key="cats[currentIndex]?.id"
          :src="cats[currentIndex]?.url"
          :alt="`Cat ${currentIndex + 1}`"
          @touchstart="onTouchStart"
          @touchend="onTouchEnd" />
      </transition>
      <div class="buttons">
        <button class="dislike" @click="dislikeCat">👈 Dislike</button>
        <button class="like" @click="likeCat">Like 👉</button>
      </div>
    </div>

    <!-- Display cats that user like -->
    <div v-else class="results">
      <h2>You liked {{ catsLike.length }} out of {{ totalCats }} cats 😺</h2>
      <div class="grid">
        <img
          v-for="cat in catsLike"
          :key="cat.id"
          :src="cat.url"
          class="liked-img"
          :alt="'Liked Cat'" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: auto;
  padding: 1rem;
  text-align: center;
}

.title {
  font-size: 2rem;
  margin-bottom: 1rem;
  color: #4b5563;
}

.swipe-hint {
  display: flex;
  justify-content: space-between;
  background-color: #f3f4f6;
  padding: 0.5rem 1rem;
  border-radius: 8px;
  margin-bottom: 1rem;
  font-weight: 500;
  color: #374151;
  font-size: 0.95rem;
}

.loading {
  font-size: 1.2rem;
  color: #6b7280;
}

.card-wrapper {
  position: relative;
}

.cat-img {
  width: 100%;
  height: auto;
  border-radius: 16px;
  object-fit: cover;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.3);
  transition: filter 0.4s ease, opacity 0.4s ease;
  user-select: none;
}

.buttons {
  margin-top: 1rem;
  display: flex;
  justify-content: space-between;
  gap: 1rem;
}

button {
  flex: 1;
  font-size: 1rem;
  padding: 10px;
  border-radius: 8px;
  border: none;
  cursor: pointer;
  color: white;
  transition: all 0.3s ease;
}

.like {
  background-color: #22c55e;
}

.dislike {
  background-color: #ef4444;
}

button:hover {
  filter: brightness(0.9);
}

.results h2 {
  margin-bottom: 1rem;
  color: #4b5563;
}

.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
  gap: 1rem;
  margin-top: 1rem;
}

.liked-img {
  width: 100%;
  height: auto;
  border-radius: 8px;
  object-fit: cover;
  aspect-ratio: 4 / 3;
  box-shadow: 0 3px 8px rgba(0, 0, 0, 0.2);
}

/* Fade transition */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease, filter 0.5s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  filter: blur(8px);
}
</style>
