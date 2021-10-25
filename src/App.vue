/* eslint-disable no-unused-vars */
<template>
  <div id="app">
    <div class="characters">
    <div class="character" v-for="character in ApiCharacters" :key="character.id">
        <h1>{{ character.name }}</h1>
        <p>{{ character.description }}</p>
        <p>price: ${{ character.price }}</p>
        <img :src="`${character.thumbnail.path}/portrait_xlarge.jpg`" alt="" />
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
      ApiCharacters: [],
    }
  },
  mounted() {
    characters.forEach(element => {
      axios
      .get(`https://gateway.marvel.com/v1/public/characters?name=${element.name}&apikey=${public_key}`)
      .then(response => {
        const charObj = response.data.data.results[0];
        charObj.price = element.price;
        this.ApiCharacters.push(charObj)
          }
        )
      .catch(error => console.log(error));
    })
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.characters {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
}

.character{
  background: rgb(219, 217, 190);
  padding: 2rem;
  margin: 3rem;
  width: calc(33% - 10rem);
}
</style>
