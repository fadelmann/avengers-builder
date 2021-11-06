<template>
  <div
    class="card"
  >
    <div
      ref="transformArea"
      class="transform-area"
      :class="[{ selected: character.selected }, { disabled: budget < character.price && !character.selected || avengers.length >= 6 && !character.selected }]"
      @click="selected"
    >
      <div class="thumbnail">
        <img
          :src="`${character.thumbnail.path}/standard_xlarge.jpg`"
          :alt="character.name"
        >
      </div>
      <h2 class="card-headline">
        {{ character.name }}
      </h2>
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
    },
  },
  mounted() {
    this.$el.addEventListener('mouseenter', this.enter);
    this.$el.addEventListener('mouseleave', this.reverse);
  },
  methods:{
    selected() {
      this.$emit('card-selected');
    },
    enter() {
      this.$el.addEventListener('mousemove', this.rotate);
    },
    rotate(e) {
      const card = {
        width: this.$el.getBoundingClientRect().width,
        height: this.$el.getBoundingClientRect().height,
        x: this.$el.getBoundingClientRect().x,
        y: this.$el.getBoundingClientRect().y,
      };
      const mousePosition = {
        x: e.clientX,
        y: e.clientY,
      };
      const Xposition = (mousePosition.x - card.x - card.width / 2);
      const rotateX = Xposition / (card.width / 2);

      const Yposition = (mousePosition.y - card.y - card.height / 2);
      const rotateY = Yposition / (card.height / 2);
      rotateY;
      if (!(this.$refs.transformArea.classList.contains('disabled'))) {
        this.$refs.transformArea.style.transform = `rotateY(${-rotateX * 20}deg) rotateX(${-rotateY * 20}deg)`;
      }
    },
    reverse() {
      this.$refs.transformArea.style.transform = 'rotateY(0deg) rotateX(0deg)';
      this.$el.removeEventListener('mousemove', this.rotate);
    },
  },
};
</script>

<style scoped>
.transform-area {
  transform-origin: center;
  transform: rotateY(0deg) rotateX(0deg);
  background: rgb(26 45 52);
  border: solid 5px rgb(38, 71, 87);
  border-radius: 10%;
  margin: 1rem;
  padding: 2rem;
  min-width: 11%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  cursor: pointer;
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
  margin:  0 0 20px 0;
  height: 150px;
}

.thumbnail img {
  border-radius: 12%;
  height: 150px;
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

.selected {
  background: rgb(255 246 120);
  border: solid 5px black;
  color: black;
}

.disabled {
  pointer-events: none;
  opacity: .5 !important;
}

.select-btn {
  background: rgb(206, 239, 255);
  margin-top: 1rem;
}

</style>
