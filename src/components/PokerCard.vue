<template>
  <div class="card-container" @click="isFlipped = !isFlipped">
    <div class="poker-card" :class="{ flipped: isFlipped }">
      <!-- 卡牌正面 -->
      <div class="card-front">
        <div class="card-suit">{{ props.suit }}</div>
        <div class="card-value">{{ props.value }}</div>
      </div>
      <!-- 卡牌背面 -->
      <div class="card-back">
        <img src="@/assets/logo512.png" alt="Card Back" />
      </div>
    </div>
  </div>
</template>

<script setup>
import {ref} from 'vue'
const props = defineProps({
  value:String,
  suit:String
})
const isFlipped = ref(false)

</script>

<style scoped>
.card-container {
  perspective: 1000px; /* 3D效果 */
}

.poker-card {
  width: 60px;
  height: 90px;
  position: relative;
  transform-style: preserve-3d;
  transition: transform 0.6s;
  cursor: pointer;
}

.poker-card.flipped {
  transform: rotateY(180deg);
}

.card-front, .card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden; 
  border-radius: 10px;
}

.card-front {
  background-color: white;
  display: flex;
  flex-direction: row;
  justify-content: left;
  align-items: top;
  border: 2px solid black;
  padding: 5px;
}

.card-back {
  background-color: white;
  transform: rotateY(180deg);
  display: flex;
  justify-content: center;
  align-items: center;
}

.card-back img {
  width: 100%;
  height: 100%;
  border-radius: 10px;
}

.card-suit{
  font-size: 16px;
  margin-right: 10px;
}

.card-value{
  font-size: 16px;
}
</style>
