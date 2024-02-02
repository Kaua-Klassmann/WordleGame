<template>
    <div v-once class="divKeyboard d-flex">
      <div class="keyboard">
        <div v-for="linha in 3" :key="linha" class="linha d-flex">
          <button v-for="letra in keyboard[linha-1]" :key="letra" class="letter text-white">
            {{ letra }}
          </button>
        </div>
      </div>
    </div>
</template>

<script>
export default {
    name: "Teclado",
    data() {
        return {
            keyboard: [
                ["Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P"],
                ["A", "S", "D", "F", "G", "H", "J", "K", "L"],
                ["ENTER", "Z", "X", "C", "V", "B", "N", "M", "⌫"]
            ],
        }
    }
}
</script>

<style scoped>
.divKeyboard {
  justify-content: center;
}

.keyboard {
  margin-top: 5vh;
}

.linha {
  gap: 0.4rem;
  justify-content: center;
  margin-top: 0.4rem;
}

.letter {
  min-height: 6vh;
  min-width: 7vw;
  background-color: #818384;
  font-size: 19px;
  border-width: 0;
}

button:active {
  background-color: #596162;
}

@media (min-width: 769px) {
  .letter {
    font-size: 22px;
    min-width: 3vw;
  }
}
</style>