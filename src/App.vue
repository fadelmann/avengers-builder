<template>
  <div id="app">
    <transition name="fade">
      <spinner
        v-if="loading && !error"
        :characters="apiCharacters"
      />
    </transition>
    <transition name="fade">
      <div
        v-if="error"
        class="error"
      >
        <div class="error-content">
          <h1>Sorry, we made a mistake. Please try to load the page again.</h1>
          <button
            class="btn error-btn"
            @click="reload"
          >
            Reload
          </button>
        </div>
      </div>
    </transition>
    <main v-if="!error">
      <h1 class="headline">
        Choose your Avengers!
      </h1>
      <hub
        :show="show"
        :budget="budget"
        :avengers="avengers"
        @remove-all="removeAll"
      />
      <div
        ref="characterGrid"
        class="characters"
      >
        <div
          v-for="character in apiCharacters"
          ref="characterRef"
          :key="character.id"
          class="character"
          :class="[{ selected: character.selected }, {disabled: budget < character.price && !character.selected || avengers.length >= 6 && !character.selected}]"
          @click="toggleSelect(character)"
        >
          <card
            :character="character"
            :avengers="avengers"
            :budget="budget"
          />
        </div>
      </div>
    </main>
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
import Hub from './components/Hub';
import Card from './components/Card';

export default {
  name: 'App',
  components: {
    Spinner,
    Hub,
    Card,
  },
  data() {
    return {
      apiCharacters: [],
      budget: 20,
      show: false,
      loading: true,
      error: false,
    };
  },
  computed: {
    avengers() {
      return this.apiCharacters.filter(c => c.selected === true);
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
        })).catch(() => this.error = true);
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
      this.budget = 20;
      this.apiCharacters.map(e => e.selected = false);
    },
    reload() {
      location.reload();
    }
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

.error {
  position: fixed;
  top: 0px;
  left: 0px;
  right: 0px;
  bottom: 0px;
  overflow: hidden;
  background: rgb(38, 71, 87);
  z-index: 2000;
  display: flex;
  justify-content: center;
  align-items: center;
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

.selected {
  background: rgb(255 246 120);
  border: solid 5px black;
  color: black;
}

.disabled {
  pointer-events: none;
}

.disabled .card{
  opacity: .5 !important;
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

.btn {
  letter-spacing: .05rem;
  cursor: pointer;
  all: unset;
  color: rgb(38, 71, 87);
  padding: .5rem 1rem;
  font-weight: bold;
}

.error-btn {
 background: white;
 cursor: pointer;
 margin-top: 1rem;
}

.select-btn {
  background: rgb(206, 239, 255);
  margin-top: 1rem;
}

.remove-btn {
  background: rgb(206, 239, 255);
  height: 20px;
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
.fade-enter, .fade-leave-to {
  opacity: 0;
}

</style>
