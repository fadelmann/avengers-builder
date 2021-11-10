<template>
  <div
    class="hub"
    :class="[{ show: show }, { smooth: !dragging }]"
    :style="y"
  >
    <div class="hub-draggable" />
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
      <div class="btn-wrapper">
        <button
          class="btn remove-btn"
          @click.stop.prevent="removeAll"
        >
          <span>Remove all</span>
        </button>
      </div>
    </div>
    <div class="avengers">
      <div
        v-for="avenger in avengers"
        :key="`avenger_${avenger.id}`"
        class="avenger"
      >
        <button
          class="btn hub-remove-btn"
          @click.stop.prevent="remove(avenger, $event)"
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
  data() {
    return {
      startY: 0,
      translateY: 0,
      currentY: 0,
      dragging: false,
      draggable: null,
    };
  },
  computed: {
    placeholders() {
      return 6 - this.avengers.length;
    },
    y() {
      return `transform: translateY(${this.translateY}px)`;
    },
  },
  mounted() {
    this.draggable = document.querySelector('.hub-draggable');
    this.draggable.addEventListener('touchstart', this.dragStart);
    this.draggable.addEventListener('touchmove', this.dragMove);
    this.draggable.addEventListener('touchend', this.dragEnd);
  },
  methods: {
    removeAll() {
      this.$emit('remove-all');
    },
    remove(avenger, e) {
      e.preventDefault();
      e.stopImmediatePropagation();
      this.$emit('remove', avenger);
    },
    dragStart(e) {
      this.startY = e.targetTouches[0].clientY;
    },
    dragMove(e) {
      this.dragging = true;
      e.preventDefault();
      this.translateY = e.targetTouches[0].clientY - this.startY + this.currentY;
    },
    dragEnd(e) {
      console.log(e);
      this.dragging = false;
      if (this.translateY < this.currentY) {
        this.translateY = -this.$el.getBoundingClientRect().height + 80;
        this.startY = 0;
      } else {
        this.translateY = 0;
      }
      this.currentY = this.translateY;
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
}

.smooth {
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

.hint {
  background: white;
  color: black;
  padding: .5rem;
}

@media screen and (max-width: 450px) {
  .hub {
    max-width: 100%;
    left: 0;
    border-radius: 0;
    padding: 1rem 0 0 0;
    bottom: -540px;
  }

  .hub:hover {
    bottom: -540px;
  }

  .hub-draggable {
    position: absolute;
    width: 3rem;
    height: 5rem;
    background: none;
    border-top: solid 3px white;
    content: '';
    top: .5rem;
    margin-left: auto;
    margin-right: auto;
    left: 0;
    right: 0;
  }

  .top {
    padding: 0;
  }

  .avengers {
    margin: 0;
    display: flex;
    flex-wrap: wrap;
  }


  .hint {
    margin: 0;
    color: white;
    background: black;
    max-width: 9.375rem;
    width: 33%;
  }

  .btn-wrapper {
    width: 33%;
  }

  .remove-btn {
    min-width: 4rem;
    margin-right: 0;
  }

  .budget-container {
    margin-left: 1rem;
    width: 33%;
    margin: 0;
  }
}
</style>
