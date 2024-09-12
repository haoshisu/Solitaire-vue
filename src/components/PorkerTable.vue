<template>
  <div class="poker-table">
    <!-- 生成7個牌堆 -->
    <div v-for="(pile, index) in piles" :key="index" class="card-box" >
      <PokerCard
        v-for="card in pile"
        :key="card.id"
        :value="card.value"
        :suit="card.suit"
        :flipped="card.flipped"

      />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import PokerCard from './PokerCard.vue';

const piles = ref([[], [], [], [], [], [], []]); // 7 個牌堆

// 初始化牌堆
const cards = generate();
splitCards(cards);



// 將卡片分到7個牌堆，每個牌堆的卡片數量遞增
function splitCards(cards) {
  let count = 1;
  let index = 0;
  for (let i = 0; i < piles.value.length; i++) {
    piles.value[i] = cards.slice(index, index + count).map((card, cardIndex) => ({
      ...card,
      flipped: !(cardIndex === count - 1), // 只有最下面的一張牌是翻開的
    }));
    index += count;
    count++;
  }
}

// 生成一副牌
function generate() {
  const suits = ['♠', '♥', '♦', '♣'];
  const cardValues = ["1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K"];
  const deck = [];
  let i = 1;
  for (const suit of suits) {
    for (const cardValue of cardValues) {
      deck.push({ id: i++, value:cardValue, suit: suit });
    }
  }
  return shuffle(deck);
}

function shuffle(deck) {
  let n = deck.length;
  for (let i = n - 1; i > 0; i--) {
    const rand = Math.floor(Math.random() * (i + 1));
    [deck[i], deck[rand]] = [deck[rand], deck[i]];
  }
  return deck;
}


</script>

  
<style scoped>
.poker-table {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: top;
    width: 1000px;
    height: 1000px;
    background-color: green;
    border: 5px solid #333;
    border-radius: 20px;
    padding: 20px;
    gap: 10px;
}

.card-box {
    border: 2px solid black ; width: 60px; height: 90px; background-color: white; border-radius: 10px;
    display: grid; grid-template-rows: repeat(13,3rem) 
}

.poker-card {
  width: 100%;
  height: 90px;
  position: relative;
  cursor: grab;
}

.hidden-card {
  background-color: gray; /* 背面牌 */
}

.dragging {
  opacity: 0.5;
}

</style>
  