<template>
  <body>
    <Cabecalho v-once/>
    <!-- Grid de plavra -->
    <div class="divBoard d-flex">
      <div class="board d-flex text-white">
        <div v-for="coluna in 5" :key="coluna" class="column d-flex">
          <div v-for="quadrado in 6" :key="quadrado">
            <span :id="'quadrado'+quadrado+coluna">
              {{ letters[quadrado-1][coluna-1] }}</span>
          </div>
        </div>
      </div>
    </div>
    <!-- Teclado -->
    <Teclado v-once/>
  </body>
</template>

<script setup>
import { ref, watch, computed, onMounted } from 'vue';

import Cabecalho from './components/Cabecalho.vue';
import Teclado from './components/Teclado.vue';

let letters = ref([
  ["", "", "", "", ""],
  ["", "", "", "", ""],
  ["", "", "", "", ""],
  ["", "", "", "", ""],
  ["", "", "", "", ""],
  ["", "", "", "", ""]
])

let wordChoice = ""
let word = ""
let tentativa = 0

const correctColor = "rgb(34, 139, 34)"
const mediumCorrectColor = "#FFD700"
const incorrectColor = "#666666"

async function fetchRandomWord(){
  try {
    const response = await fetch('../src/assets/Words.json')
    const data = await response.json()
    const words = data.words
    wordChoice = words[Math.floor(Math.random() * words.length)]
    console.log(wordChoice);
  } catch(error) {
    wordChoice = "floor"
  }
}

fetchRandomWord()

function actionButton(value) {
  if(tentativa < 6){
    if(value == "ENTER"){
      if(word.length == 5){
        domColor()
      }
    } else if(value == "⌫"){ 
      word = word.slice(0, word.length-1)
    } else if(word.length < 5){
      word = word + value.toUpperCase()
    }
    // MODIFICAR NO DOM
    for(let i = 0; i < 5; i++){
      try{
        letters.value[tentativa][i] = word[i]
      } catch {
        try{
          letters.value[tentativa][i] = ""
        } catch{null}
      }
    }
  }
}

function actionKeydown(event){
  if(tentativa < 6){
    if(event.key == "Enter"){
      if(word.length == 5){
        domColor()
      }
    } else if(event.key == "Backspace"){ 
      word = word.slice(0, word.length-1)
    } else if(word.length < 5 && (event.which > 64 && event.which < 91)){
      word = word + event.key.toUpperCase()
    }
    // MODIFICAR NO DOM
    for(let i = 0; i < 5; i++){
      try{
        letters.value[tentativa][i] = word[i]
      } catch {
        try{
          letters.value[tentativa][i] = ""
        } catch{null}
      }
    }
  }
}

onMounted(() => {
  const buttons = document.querySelectorAll("button")
  buttons.forEach(button => {
    button.addEventListener("click", () => {
      const value = button.textContent
      actionButton(value)
    })
  })
})

document.addEventListener("keydown", event => {
  actionKeydown(event)
})

function reiniciar() {
  if(confirm("Deseja jogar novamente?")){
    wordChoice = fetchRandomWord()
    tentativa = 0
    word = ""
    letters.value = [
      ["", "", "", "", ""],
      ["", "", "", "", ""],
      ["", "", "", "", ""],
      ["", "", "", "", ""],
      ["", "", "", "", ""],
      ["", "", "", "", ""]
    ]

    const spans = document.querySelectorAll('span')
    spans.forEach(span => span.style.backgroundColor = "#14161C")
  }
}

function ganhou() {
  if(word == wordChoice){
    alert("Você ganhou!")
    reiniciar()
  } else {
    if(tentativa == 5){
      alert("Você perdeu!")
      reiniciar()
      return
    }
    tentativa++
    word = ""
  }
}

function domColor() {
  const lettersInWord = {}
  const jaForam = {}

  // SOMENTE CORRETAS
  for(let letter in word){
    letter = Number(letter)
    if(isNaN(lettersInWord[word[letter]])){
      lettersInWord[word[letter]] = 1
    } else {
      lettersInWord[word[letter]]++
    }

    if(word[letter] == wordChoice[letter]){
      const selectedLetter = document.querySelector(`#quadrado${tentativa+1}${letter+1}`)
      selectedLetter.style.backgroundColor = correctColor
      if(isNaN(jaForam[word[letter]])){
        jaForam[word[letter]] = 1
      } else {
        jaForam[word[letter]]++
      }
    }
  }

  // INCORRETAS
  for(let letter in word){
    letter = Number(letter)
    const selectedLetter = document.querySelector(`#quadrado${tentativa+1}${letter+1}`)

    if(selectedLetter.style.backgroundColor != correctColor) {
      if(!(jaForam[word[letter]])) {
        jaForam[word[letter]] = 0
      }

      if(lettersInWordChoice.value[word[letter]] > jaForam[word[letter]] && 
      lettersInWord[word[letter]] > jaForam[word[letter]]) {
        selectedLetter.style.backgroundColor = mediumCorrectColor
        jaForam[word[letter]]++
      } else {
        selectedLetter.style.backgroundColor = incorrectColor
      }
    }
  }

  ganhou()
}

const lettersInWordChoice = computed(() => {
  const lettersInWordChoice = {}

  for(let i = 0; i < wordChoice.length; i++){
    if(isNaN(lettersInWordChoice[wordChoice[i]])) {
      lettersInWordChoice[wordChoice[i]] = 1
    } else {
      lettersInWordChoice[wordChoice[i]]++
    }
  }

  return lettersInWordChoice
})

watch(tentativa, () => {
  if(tentativa == 6){
    alert("Você perdeu!")
    reiniciar()
  }
})
</script>

<style scoped>
body {
  min-height: 100vh;
  max-width: 100vw;
  background-color: #14161C;
}

.divBoard {
  justify-content: center;
}

.board {
  margin-top: 9vh;
  height: 397px;
  width: 330px;
}

.column {
  flex-direction: column;
  margin-right: 5px;
}

span {
  height: 62px;
  width: 62px;
  margin-bottom: 5px;
  border: 1px solid #585858;
  display: grid;
  place-items: center;
  font-size: 28px;
}
</style>
