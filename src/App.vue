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
    <!-- Teclado -->o
    <Teclado v-once/>
  </body>
</template>

<script>
import Cabecalho from './components/Cabecalho.vue';
import Teclado from './components/Teclado.vue';

export default {
  name: "App",
  components: {
    Cabecalho,
    Teclado
  },
  data() {
    return {
      wordChoice: "",
      lettersInWordChoice: {},
      letters: [
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""]
      ],
      word: "",
      tentativa: 0,
      correctColor: "rgb(34, 139, 34)",
      mediumCorrectColor: "#FFD700",
      incorrectColor: "#666666"
    }
  },
  methods: {
    async fetchRandomWord() {
      try {
        const response = await fetch('../src/assets/Words.json')
        const data = await response.json()
        const words = data.words
        this.wordChoice = words[Math.floor(Math.random() * words.length)]
        console.log(this.wordChoice);
      } catch(error) {
        this.wordChoice =  "floor"
      }

      this.lettersInWordChoice = {}

      for(let i = 0; i < this.wordChoice.length; i++){
        if(isNaN(this.lettersInWordChoice[this.wordChoice[i]])) {
          this.lettersInWordChoice[this.wordChoice[i]] = 1
        } else {
          this.lettersInWordChoice[this.wordChoice[i]]++
        }
      }
    },
    actionButton(value) {
      if(this.tentativa < 6){
        if(value == "ENTER"){
          if(this.word.length == 5){
            this.domColor()
          }
        } else if(value == "⌫"){ 
          this.word = this.word.slice(0, this.word.length-1)
        } else if(this.word.length < 5){
          this.word = this.word + value
        }
        // MODIFICAR NO DOM
        for(let i = 0; i < 5; i++){
          try{
            this.letters[this.tentativa][i] = this.word[i]
          } catch {
            try{
              this.letters[this.tentativa][i] = ""
            } catch{null}
          }
        }
      }
    },
    actionKeydown(event){
      if(this.tentativa < 6){
        if(event.key == "Enter"){
          if(this.word.length == 5){
            this.domColor()
          }
        } else if(event.key == "Backspace"){ 
          this.word = this.word.slice(0, this.word.length-1)
        } else if(this.word.length < 5 && (event.which > 64 && event.which < 91)){
          this.word = this.word + event.key.toUpperCase()
        }
        // MODIFICAR NO DOM
        for(let i = 0; i < 5; i++){
          try{
            this.letters[this.tentativa][i] = this.word[i]
          } catch {
            try{
              this.letters[this.tentativa][i] = ""
            } catch{null}
          }
        }
      }
    },
    domColor(){
      const lettersInWord = {}
      const jaForam = {}

      // SOMENTE CORRETAS
      for(let letter in this.word){
        letter = Number(letter)
        if(isNaN(lettersInWord[this.word[letter]])){
          lettersInWord[this.word[letter]] = 1
        } else {
          lettersInWord[this.word[letter]]++
        }

        if(this.word[letter] == this.wordChoice[letter]){
          const selectedLetter = document.querySelector(`#quadrado${this.tentativa+1}${letter+1}`)
          selectedLetter.style.backgroundColor = this.correctColor
          if(isNaN(jaForam[this.word[letter]])){
            jaForam[this.word[letter]] = 1
          } else {
            jaForam[this.word[letter]]++
          }
        }
      }

      // INCORRETAS
      for(let letter in this.word){
        letter = Number(letter)
        const selectedLetter = document.querySelector(`#quadrado${this.tentativa+1}${letter+1}`)
  
        if(selectedLetter.style.backgroundColor != this.correctColor) {
          if(!(jaForam[this.word[letter]])) {
            jaForam[this.word[letter]] = 0
          }

          if(this.lettersInWordChoice[this.word[letter]] > jaForam[this.word[letter]] && 
          lettersInWord[this.word[letter]] > jaForam[this.word[letter]]) {
            selectedLetter.style.backgroundColor = this.mediumCorrectColor
            jaForam[this.word[letter]]++
          } else {
            selectedLetter.style.backgroundColor = this.incorrectColor
          }
        }
      }

      this.ganhou()
    },
    ganhou() {
      if(this.word == this.wordChoice){
        alert("Você ganhou!")
        if(confirm("Deseja jogar novamente?")){
          this.wordChoice = this.fetchRandomWord()
          this.tentativa = 0
          this.word = ""
          this.letters = [
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
      } else {
        this.tentativa++
        this.word = ""
      }
    }
  },
  mounted() {
    this.fetchRandomWord()

    const buttons = document.querySelectorAll("button")
    buttons.forEach(button => {
      button.addEventListener("click", () => {
        const value = button.textContent
        this.actionButton(value)
      })
    })

    document.addEventListener("keydown", event => {
      this.actionKeydown(event)
    })
  }
}
</script>

<style scoped>
body {
  height: 100vh;
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