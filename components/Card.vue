<template>
  <div class="card" :class="flippedStyles" @click="selectCard">
    <div class="card-face is-front">
      <img
        :src="require(`assets/game/${value}.png`)"
        :alt="value"
        class="card-image rounded"
      />
      <img
        v-if="matched"
        src="../assets/game/check.png"
        class="icon-checkmark h-5 w-5"
        alt=""
      />
    </div>
    <img
      src="../assets/game/back.png"
      width="20"
      height="20"
      class="card-face is-back object-scale-down"
    />
  </div>
</template>

<script>
import { computed } from '@nuxtjs/composition-api'
export default {
  props: {
    matched: {
      type: Boolean,
      default: false,
    },
    position: {
      type: Number,
      required: true,
    },
    value: {
      type: String,
      required: true,
    },
    visible: {
      type: Boolean,
      default: false,
    },
  },
  setup(props, context) {
    const flippedStyles = computed(() => {
      if (props.visible) {
        return 'is-flipped'
      }
    })
    const selectCard = () => {
      context.emit('select-card', {
        position: props.position,
        faceValue: props.value,
      })
    }

    return {
      flippedStyles,
      selectCard,
    }
  },
}
</script>

<style scoped>
.card {
  position: relative;
  box-shadow: 0 1px 0 #444, 0 2px 0 #373737, 0 3px 0 #555, 0 4px 0 #474747,
  0 5px 0 #666, 0 6px 1px rgba(0, 0, 0, 0.1), 0 0 5px rgba(0, 0, 0, 0.1),
  0 1px 3px rgba(0, 0, 0, 0.3), 0 3px 5px rgba(0, 0, 0, 0.2),
  0 5px 10px rgba(0, 0, 0, 0.25), 0 10px 10px rgba(0, 0, 0, 0.2),
  0 20px 20px rgba(0, 0, 0, 0.15);
  border-radius: 6px;
  transition: 0.5s transform ease-in;
  transform-style: preserve-3d;
}

.card.is-flipped {
  transform: rotateY(180deg);
}

.card-image {
  max-width: 100%;
}

.card-face {
  width: 100%;
  height: 100%;
  position: absolute;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: whitesmoke solid 2px;
  background: whitesmoke;
  z-index: 4;
  backface-visibility: hidden;
}

.card-face.is-front {
  transform: rotateY(180deg);
}
.icon-checkmark {
  position: absolute;
  right: 0;
  bottom: -5px;
}
.card.img {
  transform: translateZ(100px);
}
</style>
