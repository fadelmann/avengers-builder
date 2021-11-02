<template>
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
        <span>Remove all</span>
      </button>
    </div>
    <div class="avengers">
      <div
        v-for="avenger in avengers"
        :key="`avenger_${avenger.id}`"
        class="avenger"
      >
        <button
          class="btn hub-remove-btn"
          @click="remove(avenger)"
        >
          <span class="material-icons">close</span>
        </button>
        <img
          :src="`${avenger.thumbnail.path}/standard_fantastic.jpg`"
          :alt="avenger.name"
        >
        <h3>{{ avenger.name }}</h3>
        <h3>${{ avenger.price }}</h3>
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
</template>

<script>


export default {
  name: 'Hub',
  props: {
    show: {
      type: Boolean,
      required: false,
    },
    budget: {
      type: Number,
      required: false,
      default: 0,
    },
    avengers: {
      type: Array,
      default: () => {},
    },
  },
  computed: {
    placeholders() {
      return 6 - this.avengers.length;
    },
  },
  methods: {
    removeAll() {
      this.$emit('remove-all');
    },
    remove(avenger) {
      this.$emit('remove', avenger);
    },
  },
};
</script>

<style scoped>
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

.hub:hover {
  bottom: 0;
}

.show {
  bottom: 0;
}

.avenger {
  position: relative;
  height: 163px;
  width: 125px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}

.hub-remove-btn {
  cursor: pointer;
  background: rgb(38, 71, 87);
  border-radius: 50%;
  padding: .3rem;
  position: absolute;
  right: .3rem;
  top: .3rem;
  color: white;
  height: 1rem;
  width: 1rem;
}

.hub-remove-btn span {
  font-size: .8rem;
}
</style>
