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
      <h1
        class="headline"
        :style="{ opacity: scrollHeadlineRatio, transform: `scale(${scrollHeadlineRatio})` }"
      >
        Choose your Avengers!
      </h1>
      <hub
        :show="show"
        :budget="budget"
        :avengers="avengers"
        @remove-all="removeAll"
        @remove="remove"
      />
      <div
        ref="characterGrid"
        class="characters"
      >
        <card
          v-for="character in apiCharacters"
          ref="characterRef"
          :key="character.id"
          class="card"
          :character="character"
          :avengers="avengers"
          :budget="budget"
          @card-selected="toggleSelect(character)"
        />
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
      scrollY: 0,
    };
  },
  computed: {
    scrollHeadlineRatio() {
      return (400 - this.scrollY) / 400 > 0 ? (400 - this.scrollY) / 400 : 0;
    },
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
    document.addEventListener('scroll', () => (this.scrollY = window.scrollY));
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
      const targets = this.$refs.characterRef.map(item => {
        return item['$el'];
      });
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
      localStorage.setItem(this.apiCharacters, JSON.stringify(this.apiCharacters));
    },
    removeAll() {
      this.budget = 20;
      this.apiCharacters.map(e => e.selected = false);
    },
    remove(avenger) {
      this.apiCharacters.map(e => {
        if (e.id === avenger.id) {
          e.selected = false;
          this.budget += e.price;
        }
      });
    },
    reload() {
      location.reload();
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

main {
  margin: 0 4rem;
}

h1 {
  letter-spacing: .1rem;
  display: inline-block;
}

h3 {
  letter-spacing: .15rem;
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
  font-size: 6.25rem;
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
  max-width: 1290px;
}

.card {
  opacity: 0;
  transform: translateY(-10px);
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

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}


@media screen and (max-width: 1330px) {
  .headline {
    font-size: 5rem;
  }

  h2 {
    font-size: 1rem;
  }
}

@media screen and (max-width: 375px) {
  main {
    margin: 0 .5rem;
  }

  .headline {
    font-size: 4rem;
    padding: 2rem 0;
  }
}
</style>
