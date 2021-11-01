<template>
  <div id="app">
    <transition name="fade">
      <spinner
        v-if="loading"
        :characters="apiCharacters"
      />
    </transition>
    <h1 class="headline">
      Choose your Avengers!
    </h1>
    <div
      class="hub"
      :class="{ show: show }"
    >
      <div class="top">
        <div class="budget-container">
          <h4>Your Budget:</h4>
          <h1 class="budget">
            ${{ budget }}
          </h1>
        </div>
        <button
          class="btn remove-btn"
          @click="removeAll"
        >
          Remove all
        </button>
      </div>
      <div class="avengers">
        <div
          v-for="avenger in avengers"
          :key="`avenger_${avenger.id}`"
          class="avenger"
        >
          <img
            :src="`${avenger.thumbnail.path}/standard_xlarge.jpg`"
            :alt="avenger.name"
          >
          <h5>{{ avenger.name }}</h5>
        </div>
        <div
          v-for="(placeholder, index) in placeholders"
          :key="`placeholder${index}`"
          class="avenger"
        >
          <div class="placeholder">
            <h1>?</h1>
          </div>
          <h5>?</h5>
        </div>
      </div>
    </div>
    <div
      ref="characterGrid"
      class="characters"
    >
      <div
        v-for="character in apiCharacters"
        ref="characterRef"
        :key="character.id"
        class="character"
        :class="{ selected: character.selected }"
        @click="toggleSelect(character)"
      >
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
    </div>
  </div>
</template>

<script>
// eslint-disable-next-line no-unused-vars
import axios from 'axios';
import imagesloaded from 'imagesloaded';
import anime from 'animejs/lib/anime.es.js';
// eslint-disable-next-line no-unused-vars
// eslint-disable-next-line camelcase
import { public_key } from './marvel';
import { characters as customCharactersData } from './characters';
import Spinner from './components/Spinner';

export default {
  name: 'App',
  components: {
    Spinner,
  },
  data() {
    return {
      apiCharacters: [],
      budget: 20,
      show: false,
      loading: true,
    };
  },
  computed: {
    avengers() {
      return this.apiCharacters.filter(c => c.selected === true);
    },
    placeholders() {
      return 6 - this.avengers.length;
    },
  },
  async mounted() {
    this.apiCharacters = await Promise.all(customCharactersData.map(({ name, price }) => {
      // eslint-disable-next-line camelcase
      return axios.get(`https://gateway.marvel.com/v1/public/characters?name=${name}&apikey=${public_key}`)
        .then(r => ({
          ...r.data.data.results[0],
          price,
          selected: false,
        }));
    }));

    setTimeout(() => {
      imagesloaded(this.$refs.characterGrid, () => {
        this.loading = false;
        setTimeout(this.staggerChars, 800);
      });
    }, 4000);

  },
  methods: {
    getComputedStyle() {
      return {
        width: this.randomNumber(200),
        left: this.randomNumber(window.innerWidth),
        top: this.randomNumber(window.innerHeight),
      };
    },
    randomNumber(number) {
      return `${Math.random() * number}px`;
    },
    staggerChars() {
      const targets = this.$refs.characterRef;
      anime({
        targets,
        translateY: 0,
        opacity: 1,
        delay: anime.stagger(250), // increase delay by 100ms for each elements.
      });
    },
    toggleSelect(c) {
      if (c.selected === false && this.budget >= c.price && this.avengers.length < 6) {
        clearTimeout(this.timeOut);
        c.selected = true;
        this.budget -= c.price;
        this.show = true;
        this.timeOut = setTimeout(() => {
          this.show = false;
        }, 2000);
      } else if (c.selected === true) {
        c.selected = false;
        this.budget += c.price;
      }
    },
    removeAll() {
      this.staggerChars();
      this.budget = 20;
      this.apiCharacters.map(e => e.selected = false);
    },
  },
};
</script>

<style>
* {
  margin: 0;
}

#app {
  font-family: 'Bangers', cursive;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #ffffff;
  flex-direction: column;
  display: flex;
  justify-content: center;
  align-items: center;
  box-sizing: border-box;
  height: 100%;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
  background-image: url(./assets/bg.jpg);
}

h1 {
  letter-spacing: .1rem;
}

.spinner {
  position: fixed;
  height: 100vh;
  width: 100vw;
  background: rgb(38, 71, 87);
  top: 0;
  left: 0;
  z-index: 20;
}

.spinnerImgs {
  position: absolute;
  opacity: .5;
}

.headline {
  font-size: 100px;
  margin: 5rem 0;
  letter-spacing: .7rem;
  padding: 3rem;
  background: white;
  color: rgb(38, 71, 87);
}

.characters {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
  justify-content: center;
  align-items: center;
  margin-bottom: 400px;
}

.character {
  background: rgb(26 45 52);
  border: solid 5px rgb(38, 71, 87);
  border-radius: 10%;
  margin: 1rem;
  padding: 2rem;
  max-width: 400px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  cursor: pointer;
  transform: translateY(-10px);
  opacity: 0;
}

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

.hub {
  position: fixed;
  background: black;
  color: white;
  min-height: 220px;
  min-width: 60%;
  bottom: -180px;
  left: calc(50% - 30%);
  box-shadow: 11px -7px 11px 3px #0000004f;
  z-index: 10;
  border-radius: 8rem 8rem 0 0;
  padding: 1rem;
  transition: all ease-in-out 300ms;
}

.placeholder {
  width: 100px;
  height: 100px;
  border-radius: 100px;
  background: grey;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 10px 0;
}

.hub:hover {
  bottom: 0;
}

.show {
  bottom: 0;
}

.top {
  display: flex;
  justify-content: space-between;
  padding: 0 6rem;
}

.avengers {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  margin: 0 3rem;
}

.avenger {
  margin: 10px;
}

.avenger img{
  border-radius: 50%;
  width: 100px;
}

.select-btn {
  cursor: pointer;
  all: unset;
  background: rgb(206, 239, 255);
  color: rgb(38, 71, 87);
  padding: .5rem 1rem;
  font-weight: bold;
  margin-top: 1rem;
}

.remove-btn {
  cursor: pointer;
  all: unset;
  background: rgb(206, 239, 255);
  color: rgb(38, 71, 87);
  padding: .5rem 1rem;
  font-weight: bold;
  height: 20px;
}

.btn {
  letter-spacing: .05rem
}

.remove-btn:hover {
  cursor: pointer;
}

.select-btn:disabled {
  opacity: .5;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

</style>
