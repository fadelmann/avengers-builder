/* eslint-disable no-unused-vars */
<template>
  <div id="app">
    <h1 class="headline">Choose your Avengers!</h1>
    <div class="hub">
      <button @click="removeAll">Remove all</button>
      <h1 class="budget">${{ budget }}</h1>
      <div class="avengers">
        <div class="avenger" v-for="avenger in avengers" :key="`avenger_${avenger.id}`">          
          <img :src="`${avenger.thumbnail.path}/standard_xlarge.jpg`" :alt="avenger.name" />
          <h5>{{ avenger.name }}</h5>
        </div>
    </div>
    </div>
    <div class="characters">
      <div v-for="character in apiCharacters" :key="character.id" class="character" :class="{ selected: character.selected}" @click="toggleSelect(character)">
          <div class="thumbnail">
            <img :src="`${character.thumbnail.path}/standard_xlarge.jpg`" :alt="character.name" />
          </div>
          <h1>{{ character.name }}</h1>
          <p>price: ${{ character.price }}</p>
          <a :href="character.urls[1].url" target="_blank">To the Wiki</a>
          <button class="select-btn" :disabled="budget < character.price && !character.selected || avengers.length >= 6 && !character.selected">{{ character.selected === false ? 'Select' : 'Unselect' }}</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
// eslint-disable-next-line no-unused-vars
import { public_key, private_key } from './marvel'
import { characters } from './characters'

export default {
  name: 'App',
  data() {
    return {
      apiCharacters: [],
      budget: 20,
    }
  },
  mounted() {
    characters.forEach(element => {
      axios
      .get(`https://gateway.marvel.com/v1/public/characters?name=${element.name}&apikey=${public_key}`)
      .then(response => {
        const charObj = response.data.data.results[0];
        charObj.price = element.price;
        charObj.selected = false;
        charObj.btnText = 'Select';
        this.apiCharacters.push(charObj)
          }
        )
      .catch(error => console.log(error));
    })
  },
  methods: {
    toggleSelect(c) {
        if(c.selected === false && this.budget >= c.price && this.avengers.length < 6) {
          c.selected = true;
          this.budget -= c.price;
        } else if (c.selected === true) {
          c.selected = false;
          this.budget += c.price;
        }
    },
    removeAll() {
      this.budget = 20;
      this.apiCharacters.map(e => e.selected = false);
    }
  },
  computed: {
    avengers() {
      return this.apiCharacters.filter(c => c.selected === true);
    }
  }
}
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
  background-color: #9f0013;
  background-image: url(./assets/bg.jpg);
  height: 100%;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
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
  background: rgb(255, 249, 169);
  border: solid 5px black;
}

.avenger-thubmnail{
  border-radius: 50%;
  width: 100px;
}

.hub {
  position: fixed;
  background: black;
  color: white;
  min-height: 150px;
  min-width: 60%;
  bottom: 0;
  left: calc(50% - 30%);
  box-shadow: 11px -7px 11px 3px #0000004f;
  z-index: 10;
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

.select-btn:disabled {
  opacity: .3;
}

</style>
