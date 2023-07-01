<script setup>
import { ref, onMounted, computed, watch } from "vue";
const toWatch = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);
const toWatch_asc = computed(() =>
  toWatch.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

const addToWatchlist = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  toWatch.value.push({
    content: input_content.value,
    category: input_category.value,
    watched: false,
    createdAt: new Date().getTime(),
  });
};
watch(
  toWatch,
  (newVal) => {
    localStorage.setItem("toWatch", JSON.stringify(newVal));
  },
  { deep: true }
);
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  toWatch.value = JSON.parse(localStorage.getItem("toWatch")) || [];
});

const removeToWatch = (watch) => {
  toWatch.value = toWatch.value.filter((t) => t !== watch);
};
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Create your Watchlist</h3>
      <form @submit.prevent="addToWatchlist">
        <h4>What's on your watchlist</h4>
        <input
          type="text"
          placeholder="e.g Fight Club"
          v-model="input_content"
        />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="movie"
              id="category1"
              v-model="input_category"
            />
            <span class="bubble movie"></span>
            <div>Movie</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="anime"
              id="category2"
              v-model="input_category"
            />
            <span class="bubble anime"></span>
            <div>Anime</div>
          </label>
        </div>
        <input type="submit" value="add" />
      </form>
    </section>
    <section class="todo-list">
      <h3>WATCHLIST</h3>
      <div class="list">
        <div
          v-for="watch in toWatch_asc"
          :class="`todo-item ${watch.watched && 'watched'}`"
        >
          <label>
            <input type="checkbox" v-model="watch.watched" />
            <span :class="`bubble ${watch.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="watch.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeToWatch(watch)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
