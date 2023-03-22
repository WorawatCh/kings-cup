<script setup>
const props = defineProps({
    rules:Object,
    playerIndex:Number,
    drawingDeck:Array,
    playerName:Array,
    kingCard:Array
});
</script>

<template>
    <div class="cards" >
            <img class="img-card w-20"  :src="drawingDeck[0].image" alt=""  >
                <div class="container detail-container mt-3">
                    <h3 class="rule-text">{{ rules[drawingDeck[0].value] }}</h3>
                    <h3>{{ playerName[playerIndex] }} Turn</h3>
                </div>
        </div>
</template>

<style scoped>
.cards{
    color: white;
}
 .cards:active{
    -webkit-animation: flip-out-hor-top 1.5s cubic-bezier(0.550, 0.085, 0.680, 0.530) both;
	        animation: flip-out-hor-top 1.5s cubic-bezier(0.550, 0.085, 0.680, 0.530) both;
 }
 @-webkit-keyframes flip-out-hor-top {
  0% {
    -webkit-transform: rotateX(0);
            transform: rotateX(0);
    opacity: 1;
  }
  100% {
    -webkit-transform: rotateX(70deg);
            transform: rotateX(70deg);
    opacity: 0;
  }
}
@keyframes flip-out-hor-top {
  0% {
    -webkit-transform: rotateX(0);
            transform: rotateX(0);
    opacity: 1;
  }
  100% {
    -webkit-transform: rotateX(70deg);
            transform: rotateX(70deg);
    opacity: 0;
  }
}
 .cards{
    -webkit-animation: flip-in-hor-top  1.5s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;
	        animation: flip-in-hor-top  1.5s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;
}
 @-webkit-keyframes flip-in-hor-top {
  0% {
    -webkit-transform: rotateX(-80deg);
            transform: rotateX(-80deg);
    opacity: 0;
  }
  100% {
    -webkit-transform: rotateX(0);
            transform: rotateX(0);
    opacity: 1;
  }
}
@keyframes flip-in-hor-top {
  0% {
    -webkit-transform: rotateX(-80deg);
            transform: rotateX(-80deg);
    opacity: 0;
  }
  100% {
    -webkit-transform: rotateX(0);
            transform: rotateX(0);
    opacity: 1;
  }
}
</style>