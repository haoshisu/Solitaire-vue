<template>
  <div class="poker-table">
    <!-- 生成7個牌堆 -->
    <div v-for="(pile, index) in piles.slice(0, 7)" :key="index" class="card-box" @dragover.prevent @drop="onDrop(index)" >
      <PokerCard
        v-for="(card, cardIndex) in pile"
        :key="card.id"
        :value="card.value"
        :suit="card.suit"
        :flipped="card.flipped"
        draggable="true"
        @dragstart="onDragStart(card, index, cardIndex)"
      />
    </div>
    <!-- 剩下的牌 -->
    <div class="card-box-norepeat" @click="drawCard">
      <div class="card-stack">
        <PokerCard
          v-for="card in piles[7]"
          :key="card.id"
          :value="card.value"
          :suit="card.suit"
          :flipped="card.flipped"
          draggable="true"
          class="card"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import PokerCard from './PokerCard.vue';

const piles = ref([[], [], [], [], [], [], [],[]]); // 7 個牌堆
const draggedCard = ref(null); // 被拖動的卡片
const draggedPileIndex = ref(null); // 被拖動的卡片來自哪個牌堆
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
  piles.value[piles.value.length - 1] = cards.slice(index).map(card => ({
    ...card,
    flipped: true // 剩餘卡片默認是背面的
  }));
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

//洗牌
function shuffle(deck) {
  let n = deck.length;
  for (let i = n - 1; i > 0; i--) {
    const rand = Math.floor(Math.random() * (i + 1));
    [deck[i], deck[rand]] = [deck[rand], deck[i]];
  }
  return deck;
}

// 當卡片開始被拖動時，記錄卡片的資訊和它來自的牌堆
function onDragStart(card, pileIndex, event) {
  if (card.flipped) {
    event.preventDefault(); // 阻止拖拽
  }
  draggedCard.value = card;
  draggedPileIndex.value = pileIndex;
}

// 當卡片放到新的牌堆時，處理卡片移動
function onDrop(targetPileIndex) {
  // console.log("targetPileIndext",targetPileIndex)
  if (draggedCard.value && draggedPileIndex.value !== targetPileIndex) {
    const sourcePile = piles.value[draggedPileIndex.value];
    const targetPile = piles.value[targetPileIndex];

    if (canStackCards(targetPile, draggedCard.value)){
      // 將卡片從源牌堆移除
      piles.value[draggedPileIndex.value] = sourcePile.filter(c => c.id !== draggedCard.value.id);
      // 檢查是否有牌 最後一張翻開
      if(piles.value[draggedPileIndex.value].length > 0){
        const lastCardIndex = piles.value[draggedPileIndex.value].length -1
        piles.value[draggedPileIndex.value][lastCardIndex ].flipped = false
      }
      // 將卡片放到目標牌堆
      targetPile.push(draggedCard.value);
      // console.log("pp",draggedCard.value.suit)

      // 重置被拖動的卡片資訊
      draggedCard.value = null;
      draggedPileIndex.value = null;
    }
  }
}

function canStackCards(targetPile,draggedCard){
  // console.log("targetPile",targetPile.length)
  // console.log("draggedCard",draggedCard)
  //空牌堆都可以放
  if(targetPile.length === 0){
    return true
  }
 
  const topCard = targetPile[targetPile.length -1]
  // console.log("topcard",topCard)
  const blackSuits = ['♠','♣']
  const redSuits = ['♥', '♦']

  const isDifferentColor = (blackSuits.includes(topCard.suit) && redSuits.includes(draggedCard.suit) ||
    redSuits.includes(draggedCard.suit) && blackSuits.includes(topCard.suit)
  )
  // const cardValues = ['K', 'Q', 'J', '10', '9', '8', '7', '6', '5', '4', '3', '2', '1']
  // console.log(cardValues.indexOf(draggedCard.value))
  // const isDecreasing = cardValues.indexOf(draggedCard.value) === cardValues.indexOf(topCard.value) - 1;

  return isDifferentColor // && isDecreasing;
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
.card-box-norepeat{
  border: 2px solid black ; width: 60px; height: 90px; background-color: white; border-radius: 10px;
  position: relative; 
  
}

.card-stack {
  position: relative;
  width: 100px;
  height: 140px; /* 調整以適應卡片大小 */
}

.card {
  position: absolute; /* 絕對定位，使牌可以重疊 */
  top: 0;
  left: 0;
  transform: translateY(calc(var(--card-index) * 10px)); /* 每張牌垂直偏移 */
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
  