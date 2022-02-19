<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  // type inference enabled
  data() {
    return {
      currentMenu: 0,
      game: {
        maxRounds: -1,
        currentRound: -1,
        maxNumber: -1,
        currentNumber: -1,
        ended: false,
        lost: false,
        message: "",
        userNumber: 0,
      },
    };
  },
  methods: {
    checkResult() {
      if (this.game.userNumber === this.game.currentNumber) {
        this.game.message = `Geschafft! Du hast die richtige Zahl nach ${this.game.currentRound} Versuchen erraten`;
        this.game.ended = true;
      } else if (this.game.userNumber < this.game.currentNumber) {
        this.game.message = `Deine gewählte Zahl ist zu niedrig. ${this.game.currentRound}/${this.game.maxRounds}`;
        this.game.currentRound++;
      } else {
        this.game.message = `Deine gewählte Zahl ist zu hoch. ${this.game.currentRound}/${this.game.maxRounds}`;
        this.game.currentRound++;
      }
      if (this.game.currentRound > this.game.maxRounds) {
        this.game.message += `Du hast alle Züge aufgebraucht, die richtige Zahl war ${this.game.currentNumber}.`;
        this.game.ended = true;
        this.game.lost = true;
      }
    },
    setRandomNumber(max: number) {
      return Math.floor(Math.random() * (max - 0 + 1) + 0);
    },
    setGameDifficulty(difficulty: number) {
      this.currentMenu = 3;
      this.game.ended = false;
      this.game.lost = false;
      this.game.userNumber = 0;
      this.game.message = "Wähle deine erste Zahl";
      switch (difficulty) {
        case 0:
          this.game.maxRounds = 8;
          this.game.currentRound = 1;
          this.game.maxNumber = 20;
          this.game.currentNumber = this.setRandomNumber(this.game.maxNumber);
          break;
        case 1:
          this.game.maxRounds = 6;
          this.game.currentRound = 1;
          this.game.maxNumber = 50;
          this.game.currentNumber = this.setRandomNumber(this.game.maxNumber);
          break;
        case 2:
          this.game.maxRounds = 4;
          this.game.currentRound = 1;
          this.game.maxNumber = 80;
          this.game.currentNumber = this.setRandomNumber(this.game.maxNumber);
          break;
      }
    },
  },
});
</script>

<template>
  <div id="main">
    <div class="grid grid-cols-3">
      <div></div>
      <div>
        <b>Guessing game</b><br /><br />
        <div id="mainmenu" v-if="currentMenu === 0">
          <button @click="currentMenu = 1">Spiel starten</button><br />
          <button>Optionen</button><br />
          <button>Beenden</button>
        </div>
        <div id="modeselect" v-if="currentMenu === 1">
          <button @click="currentMenu = 2">PvE</button><br />
          <button disabled>PvP</button><br />
        </div>
        <div id="difficultyselect" v-if="currentMenu === 2">
          <button @click="setGameDifficulty(0)">Leicht</button><br />
          <button @click="setGameDifficulty(1)">Mittel</button><br />
          <button @click="setGameDifficulty(2)">Schwer</button><br />
        </div>
        <div id="gametime" v-if="currentMenu === 3">
          Alles klar, du hast {{ game.maxRounds }} Züge Zeit um eine Zahl
          zwischen 0 und {{ game.maxNumber }} zu finden.<br />
          Computer: {{ game.message }}<br />
          <div v-if="!game.lost">
            <br />
            <input
              type="number"
              min="0"
              :max="game.maxNumber"
              v-model="game.userNumber"
              class="border-b w-10"
            /><button
              class="border ml-2 pl-2 pr-2"
              @click="checkResult"
              :disabled="game.ended"
            >
              Prüfen
            </button>
          </div>
        </div>
        <br />
        <button v-if="currentMenu > 0" @click="currentMenu -= 1">Zurück</button>
      </div>
      <div></div>
    </div>
  </div>
</template>

<style></style>
