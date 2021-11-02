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
    character: {
      type: Object,
      default: () => {},
    },
    budget: {
      type: Number,
      default: null,
    },
    avengers: {
      type: Array,
      default: () => [],
    }
  },
  methods: {
  },
};
</script>

<style scoped>
.price {
  position: absolute;
  top: -15px;
  right: -15px;
  background: rgb(255 246 120);
  color: black;
  width: 50px;
  height: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  font-size: 1.5rem;
}

.description {
  margin-top: .5rem;
}

.wiki-link {
  color: white;
}

.thumbnail {
  border-radius: 12%;
  transition: all ease-in-out 300ms;
  box-shadow: 0 0 0 black;
  margin:  0 0 40px 0;
  height: 200px;
}

.thumbnail img {
  border-radius: 12%;
  height: 200px;
}

.character:hover {
  border: solid 5px#ffffff;
}

.character:hover .thumbnail {
  transform: translate(-5px, -5px);
  box-shadow: black;
  box-shadow: 20px 20px 0 black;
}

.selected {
  background: rgb(255 246 120);
  border: solid 5px black;
  color: black;
}

.selected .thumbnail {
  transform: translate(-5px, -5px);
  box-shadow: black;
  box-shadow: 20px 20px 0 black;
}

.selected .select-btn {
  background: black;
  color: rgb(255 246 120);
}

.selected .wiki-link {
  color: black;
}

.selected .price {
  color: rgba(0, 0, 0, 0.26);
  background: rgb(108, 149, 170);
}

.avenger-thubmnail {
  border-radius: 50%;
  width: 100px;
}

</style>
