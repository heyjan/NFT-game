<template>
  <div class="app h-screen flex-col items-center justify-center">
    <img
      class="heroimage h-32 block ml-auto mr-auto pb-4"
      src="@/assets/game/NFTAPESLOGO2.png"
    />
    <button
      v-if="newPlayer"
      @click="startGame"
      class="Shuffle inline-block my-10 flex items-center m-auto px-6 py-2 text-xs font-medium leading-6 text-center text-white uppercase transition rounded-full shadow ripple hover:shadow-lg hover:bg-pink-600 focus:outline-none"
    >
      Start Game
    </button>
    <button
      v-else
      @click="restartGame"
      class="Shuffle inline-block my-10 flex items-center px-6 py-2 m-auto text-xs font-medium leading-6 text-center text-white uppercase transition rounded-full shadow ripple hover:shadow-lg hover:bg-pink-600 focus:outline-none"
    >
      Restart Game
    </button>
    <div class="wrapper flex">
      <transition-group
        tag="section"
        class="game-board m-3 p-8 rounded-xl ml-auto mr-auto"
        name="shuffle-card"
      >
        <Card
          v-for="card in cardList"
          :key="`${card.value}-${card.variant}`"
          :matched="card.matched"
          :value="card.value"
          :visible="card.visible"
          :position="card.position"
          @select-card="flipCard"
        />
      </transition-group>
    </div>
  </div>
</template>

<script>
import _ from 'lodash'
import { computed, ref, watch, set } from '@nuxtjs/composition-api'
import Card from '~/components/Card'
import { launchConfetti } from '../plugins/confetti'
export default {
  name: 'ape',
  components: { Card },
  setup() {
    const cardList = ref([])
    const userSelection = ref([])
    const newPlayer = ref(true)

    const startGame = () => {
      newPlayer.value = false

      restartGame()
    }

    const status = computed(() => {
      if (remainingPairs.value === 0) {
        return 'Player wins!'
      } else {
        return `Remaining Pairs: ${remainingPairs.value}`
      }
    })

    const remainingPairs = computed(() => {
      const remainingCards = cardList.value.filter(
        (card) => card.matched === false
      ).length

      return remainingCards / 2
    })

    const restartGame = () => {
      cardList.value = _.shuffle(cardList.value)

      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          matched: false,
          position: index,
          visible: false,
        }
      })
    }

    const cardItems = [
      'daan',
      'cobie',
      'badass',
      'mcdonaldsf',
      'mcdonaldsm',
      'emoji',
      'trump',
      'love',
    ]

    cardItems.forEach((item) => {
      cardList.value.push({
        value: item,
        variant: 1,
        visible: true,
        position: null,
        matched: false,
      })
      cardList.value.push({
        value: item,
        variant: 2,
        visible: true,
        position: null,
        matched: false,
      })
    })

    cardList.value = cardList.value.map((card, index) => {
      return {
        ...card,
        position: index,
      }
    })

    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true

      if (userSelection.value[0]) {
        if (
          userSelection.value[0].position === payload.position &&
          userSelection.value[0].faceValue === payload.faceValue
        ) {
        } else {
          set(userSelection.value, 1, payload)
        }
      } else {
        set(userSelection.value, 0, payload)
      }
    }

    watch(remainingPairs, (currentVaulue) => {
      if (currentVaulue === 0) {
        launchConfetti()
      }
    })

    watch(
      userSelection,
      (currentValue) => {
        if (currentValue.length === 2) {
          const cardOne = currentValue[0]
          const cardTwo = currentValue[1]
          if (cardOne.faceValue === cardTwo.faceValue) {
            cardList.value[cardOne.position].matched = true
            cardList.value[cardTwo.position].matched = true
          } else {
            setTimeout(() => {
              cardList.value[cardOne.position].visible = false
              cardList.value[cardTwo.position].visible = false
            }, 1800)
          }
          userSelection.value.length = 0
        }
      },
      { deep: true }
    )
    return {
      cardList,
      flipCard,
      userSelection,
      status,
      restartGame,
      startGame,
      newPlayer,
    }
  },
}
</script>

<style scoped>
html,
body {
  margin: 0;
  padding: 0;
}

.app {
  background-color: #4cd6bc;
}

.game-board {
  position: relative;
  -webkit-box-shadow: 0 1px 4px rgba(0, 0, 0, 0.3),
  0 0 40px rgba(0, 0, 0, 0.1) inset;
  -moz-box-shadow: 0 1px 4px rgba(0, 0, 0, 0.3),
  0 0 40px rgba(0, 0, 0, 0.1) inset;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
  display: grid;
  grid-template-columns: repeat(4, 130px);
  grid-template-rows: repeat(4, 130px);
  grid-column-gap: 25px;
  grid-row-gap: 25px;
  justify-content: center;
  background-image: url('../assets/game/wood.png');
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

@media only screen and (max-width: 600px) {
  body {
    grid-template-columns: repeat(4, 80px);
    grid-template-rows: repeat(4, 80px);
  }
}

.game-board:before,
.game-board:after {
  content: '';
  position: absolute;
  z-index: -1;
  -webkit-box-shadow: 0 0 20px rgba(0, 0, 0, 0.8);
  -moz-box-shadow: 0 0 20px rgba(0, 0, 0, 0.8);
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.8);
  top: 10px;
  bottom: 10px;
  left: 0;
  right: 0;
  -moz-border-radius: 100px / 10px;
  border-radius: 100px / 10px;
}
.game-board:after {
  right: 10px;
  left: auto;
  -webkit-transform: skew(8deg) rotate(3deg);
  -moz-transform: skew(8deg) rotate(3deg);
  -ms-transform: skew(8deg) rotate(3deg);
  -o-transform: skew(8deg) rotate(3deg);
  transform: skew(8deg) rotate(3deg);
}
.Shuffle {
  background-color: #a856ef;
}
.shuffle-card-move {
  transition: transform 0.8s ease-in;
}
</style>
