<template>
    <div class="poker-table">
      <!-- <img src="../assets/logo512.png" alt=""> -->
        <div class="card-box">
            <PokerCard v-for="card in cards" :key="card.id" :value="card.value" :suit="card.suit" />
        </div>
        <div class="card-box"></div>
        <div class="card-box"></div>
        <div class="card-box"></div>
        <div class="card-box"></div>
        <div class="card-box"></div>
    </div>
</template>
  
<script setup>
//   import {ref} from 'vue'
  import PokerCard from './PokerCard.vue'
  const cards = generate()
  function generate(){
    const suits = ['♠', '♥', '♦', '♣']
    const cardValues = ["J","Q","K"] 
    // "1","2","3","4","5","6","7","8","9","10",
    const deck = []
    let i = 1
    for(const suit of suits){
        for(const cardValue of cardValues){
            deck.push({id:i++, value:cardValue, suit:suit})
        }
    }
    return shuffle(deck)
  }
  function shuffle(deck) {
  let n = deck.length;
  for (let i = n - 1; i > 0; i--) {
    const rand = Math.floor(Math.random() * (i + 1)); 
    [deck[i], deck[rand]] = [deck[rand], deck[i]]; 
  }
  return deck
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

</style>
  