<template>
  <div class="hub">
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
            :src="`${avenger.thumbnail.path}/standard_fantastic.jpg`"
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
  },
};
</script>

<style scoped>

</style>
