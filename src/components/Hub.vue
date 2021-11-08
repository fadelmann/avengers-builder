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
      <div class="hint">
        <h3>Select {{ placeholders }} more Avengers</h3>
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
  max-width: 60%;
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

.top {
  display: flex;
  align-items: center;
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

.remove-btn {
  background: rgb(206, 239, 255);
  height: 20px;
  cursor: pointer;
}

.hub-remove-btn {
  cursor: pointer;
  background: rgb(38, 71, 87);
  border-radius: 50%;
  padding: .3rem;
  position: absolute;
  right: .6rem;
  top: .6rem;
  color: white;
  height: 1rem;
  width: 1rem;
}

.hub-remove-btn span {
  font-size: .8rem;
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
  margin: 10px 0;
}

@media screen and (max-width: 450px) {
  .hub {
    max-width: 100%;
    left: 0;
    border-radius: 2rem 2rem 0 0;
    padding: 1rem 0 0 0;
  }

  .top {
    padding: 0;
  }

  .avengers {
    overflow-y: scroll;
    margin: 0;
    justify-content: flex-start;
  }

  .hint {
    margin: 0 .4rem;
  }

  .remove-btn {
    padding: .2rem .5rem;
    min-width: 4rem;
    margin-right: 1rem;
  }

  .budget-container {
    margin-left: 1rem;
  }
}
</style>
