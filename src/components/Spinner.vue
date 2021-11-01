<template>
  <div class="spinner">
    <img
      v-for="(character, index) in shuffledChars"
      :key="`${character.id}_spinner`"
      class="spinnerImgs"
      :src="`${character.thumbnail.path}/standard_xlarge.jpg`"
      :style="getComputedStyle(index)"
    >
  </div>
</template>

<script>
import random from 'lodash/random';
import shuffle from 'lodash/shuffle';

export default {
  name: 'Spinner',
  props: {
    characters: {
      type: Array,
      required: true,
    },
  },
  computed: {
    shuffledChars() {
      return shuffle(this.characters);
    },
  },
  methods: {
    getComputedStyle(index) {
      return {
        width: this.randomNumber(200),
        left: this.randomNumber(window.innerWidth, 0.2, 0.8),
        top: this.randomNumber(window.innerHeight, 0.2, 0.8),
        'animation-delay': (index * 200) + 'ms',
      };
    },
    randomNumber: (number, low = 0.5, high = 1.5) => `${random(low, high) * number}px`,
  },
};
</script>

<style scoped>
.spinner {
  position: fixed;
  top: 0px;
  left: 0px;
  right: 0px;
  bottom: 0px;
  overflow: hidden;
}

.spinnerImgs {
  position: absolute;
  animation-duration: 500ms;
  animation-name: popIn;
  animation-fill-mode: forwards;
  animation-timing-function: ease-out;
  opacity: 0;
  border-radius: 4px;
}

@keyframes popIn {
  0% {
    opacity: 0;
    transform: scale(1);
  }

  100% {
    opacity: 1;
    transform: scale(1.2);
  }
}
</style>
