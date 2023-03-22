<script setup>
import { ref,onBeforeUpdate, onUpdated } from 'vue';
import InputText from 'primevue/inputtext';
import CardContent from '../components/CardContent.vue'

const onCardAnimation = ref(false)
const isStart = ref(false)
const isReady = ref(false)
const isFirstDraw = ref(false)
const deckTemplate = ref()
const gamingDeck = ref({
    "success": false, 
    "deck_id": "", 
    "cards": [], 
    "remaining": 0
    } 
)
const drawingDeck = ref([])
const cardIndex = ref(0)
const playerIndex = ref(0)
const playerName = ref([])
const kingCard = ref([])
const rules = ref({
    'ACE':'Waterfall',
    '2':'Choose 2 (pick other 2)',
    '3':'Three for me',
    '4':'Give 2 take 2',
    '5':'Make a rules',
    '6':'Thumb Master',
    '7':'Heaven',
    '8':'Mate',
    '9':'Bust a Rhyme',
    '10':'Categoeries',
    'QUEEN': 'Girls drink',
    'JACK': 'Guys drink',
    'KING': 'King',
})

function shufflePlayer(array){
    for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
}

async function onCreateDeck(){
    await fetch("https://deckofcardsapi.com/api/deck/new/shuffle/?deck_count=1")
    .then( (response) =>  response.json())
    .then((data) =>  {
        deckTemplate.value = {...data};
        console.log('deckTemplate',deckTemplate.value)
    })

    fetch("https://deckofcardsapi.com/api/deck/"+deckTemplate.value.deck_id+"/draw/?count=52")
    .then((response) => response.json())
    .then((data) => {
        gamingDeck.value =  {...data};
        if(gamingDeck.value.cards.length != 0){
            gamingDeck.value.cards = data.cards
            gamingDeck.value.remaining = data.remaining
            drawingDeck.value = data.cards
        }
        console.log('drawingDeck',drawingDeck.value)
        
    })
}
function onDarwCard(){
    isFirstDraw.value = true
    shufflePlayer(playerName.value)
    onNextPlayer()
}
function onStart(){
    isStart.value = !isStart.value
    onCreateDeck()
}

function onNextCard(){
    setTimeout( function(){
        drawingDeck.value.splice(0, 1);
    },200)
    
    if(drawingDeck.value[0].value == 'KING'){
        kingCard.value.push(drawingDeck.value[cardIndex.value])
    }
    setTimeout( function(){
        onNextPlayer()
    },200)
  
}

function onNextPlayer(){
    playerIndex.value++
    if( playerIndex.value == playerName.value.length){
        playerIndex.value = 0
    }
}
function onAddPlayer(event){
    playerName.value.push("")
}
function onDeletePlayer(index){
    playerName.value.splice(index, 1);
}
function onReady(){
    isReady.value = true
}
function checkEndGame(){
    if(cardIndex.value !=0 && cardIndex.value == drawingDeck.value.length){
        return true
    }
    if( cardIndex.value !=0 &&  kingCard.value.length == 4 && cardIndex.value != drawingDeck.value.length){
        return true
    }
    return false
}

function onResetGame(){
    onStart()
}

</script>

<template>
    <div class="menu-container text-center align-middle">
        <div class="header">
            <span>Kings Cup</span>
        </div>
        <div class="button-header" v-if="!isStart">
            <button type="button" class="btn btn-primary btn-lg start-btn mt-3" @click="onStart()">Start</button>
        </div>
        <div class="button-header" v-if="isReady">
            <img id="card" class="img-back-card" role="button" src="/src/assets/back.png" alt=""  @click="onDarwCard()"  v-if="!isFirstDraw">
            <div class="container detail-container mt-3"  v-if="!isFirstDraw">
                <h3>{{ playerName[playerIndex] }} Turn</h3>
            </div>
        </div>

        <div class="player mt-4"  v-if="!isReady && isStart">
            <button type="button" class="btn btn-primary start-btn" @click="onAddPlayer(event)">Add Player</button>
            <div class="container mt-4">
                <div class="row">
                    <template v-for="(item,index) in playerName" :key="item">
                        <div class="input-column col-6 text-center mb-2">
                            <InputText type="text" v-model="playerName[index]" placeholder="Enter Name" class="name-input w-100"/>
                        </div>
                        <div class="btn-column col-6 text-center mb-2" >
                            <button type="button" class="btn btn-primary start-btn input-btn" @click="onDeletePlayer(index)">Delete Player</button>
                        </div>
                    </template>
                </div>
            </div>
            <button type="button" class="btn btn-primary start-btn mt-4" @click="onReady()" :disabled="playerName.length ==0 || playerName[0] == ''">Ready</button>
        </div>

        <CardContent  @click="onNextCard()" v-if="isFirstDraw && !checkEndGame()" :drawingDeck="drawingDeck" :rules="rules" :playerName="playerName" 
        :playerIndex="playerIndex" :kingCard="kingCard"></CardContent>

        <div class="container endgame mt-3" v-if="!checkEndGame()">
            <h1 class="mt-2 mb-2">Game Over</h1>
        </div>

    </div>
</template>

<style scoped>
.img-back-card{
    width: 73%;
}
@media only screen and (max-width: 700px) {
    .header span {
        font-size: 50px !important;
    }
}
.endgame{
    background-color: white;
}
.detail-container{
    color: white;
 }
.img-back-card:active{
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
.img-back-card{
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
 .header span {
    font-size: 70px;
    font-weight: bold;
    color: white;
}
.button-header .btn,
.player .btn,
.endgame .restart-btn{
    /* top: 30%; */
    /* right: 50%; */
    background-color: #B90B0B ;
    color: white;
    border-color: white;
    box-shadow: 1.2em 2em 3em rgba(0, 0, 0, 0.2);
}
.start-btn:hover,
.restart-btn:hover{
    background-color: #8C0909 ;
}
.start-btn:active,
.restart-btn:active{
    background-color: #8C0909 !important ;
    border-color: white !important;
}
.menu-container{
    width: 100%;
}
</style>
