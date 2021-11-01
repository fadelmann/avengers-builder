<template>
  <div class="card">
    <div class="thumbnail">
      <img
        :src="`${character.thumbnail.path}/standard_xlarge.jpg`"
        :alt="character.name"
      >
    </div>
    <h1 class="card-headline">
      {{ character.name }}
    </h1>
    <div class="description">
      <a
        class="wiki-link"
        :href="character.urls[1].url"
        target="_blank"
      >To the Wiki</a>
    </div>
    <button
      class="btn select-btn"
      :disabled="budget < character.price && !character.selected || avengers.length >= 6 && !character.selected"
    >
      {{ character.selected === false ? 'Select' : 'Unselect' }}
    </button>
    <div class="price">
      <span v-if="!character.selected">${{ character.price }}</span>
      <span
        v-else
        class="material-icons"
      >check_circle</span>
    </div>
  </div>
</template>

<script>


export default {
  name: 'Card',
  props: {
  },
  methods: {
  },
};
</script>

<style scoped>

</style>
