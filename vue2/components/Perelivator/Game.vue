<template>
  <div class="game">
    <h1>Игра: Переливатор</h1>
    <div class="game__test-tubes">
      <TestTube 
        v-for="(tube, index) in tubes" 
        :key="index" 
        :layers="tube" 
        :capacity="MAX_LAYERS"
        :selected="selectedTube === index" 
        @click="() => selectTube(index)"
      />
    </div>
    <div class="game__controls">
      <button 
        class="game__reset-button" 
        @click="() => resetGame()">
        Перезапустить
      </button>
      <p 
        v-if="isGameOver">
        Игра завершена! Все цвета разделены!
      </p>
    </div>
  </div>
</template>

<script>
import TestTube from "./TestTube.vue";

export default {
  name: "Game",
  components: {
     TestTube
  },
  data() {
    return {
      MAX_LAYERS: 4, 
      tubes: [], 
      selectedTube: null
    };
  },
  computed: {
    isGameOver() {
      return this.tubes.every(
        (tube) => tube.length === 0 || new Set(tube).size === 1
      );
    }
  },
  methods: {
    generateTubes() {
      const colors = ["red", "blue", "yellow", "green", "pink"];
      const totalLayers = Math.floor((this.MAX_LAYERS * (colors.length - 1)) / 2); 
      const layers = [];

      for (let i = 0; i < totalLayers; i++) {
        layers.push(colors[Math.floor(Math.random() * colors.length)]);
      }

      const tubeCount = colors.length; 
      const tubes = Array(tubeCount)
        .fill(null)
        .map(() => []);

      while (layers.length > 0) {
        const tubeIndex = Math.floor(Math.random() * (tubeCount - 1));
        if (tubes[tubeIndex].length < this.MAX_LAYERS) {
          tubes[tubeIndex].push(layers.pop());
        }
      }

      this.tubes = tubes;
    },
    selectTube(index) {
      if (this.selectedTube === null) {
        this.selectedTube = index;
      } else {
        this.pourLayer(this.selectedTube, index);
        this.selectedTube = null;
      }
    },
    pourLayer(from, to) {
      const source = this.tubes[from];
      const target = this.tubes[to];

      if (source.length === 0 || target.length >= this.MAX_LAYERS) return;

      const layerColor = source[source.length - 1];

      const targetFillPercentage = (target.length / this.MAX_LAYERS) * 100;

      const layersToPour = [];

      while (source.length > 0 && source[source.length - 1] === layerColor) {
        layersToPour.push(source.pop());
      }

      const targetSpaceLeftPercentage = 100 - targetFillPercentage;

      if (layersToPour.length * 100 / this.MAX_LAYERS > targetSpaceLeftPercentage) {
        const layersToMove = Math.floor(targetSpaceLeftPercentage * this.MAX_LAYERS / 100);
        const remainingLayers = layersToPour.slice(layersToMove);
        target.push(...layersToPour.slice(0, layersToMove));
        source.push(...remainingLayers);
      } else {
        target.push(...layersToPour);
      }
    },
    resetGame() {
      this.selectedTube = null;
      this.generateTubes();
    },
  },
  mounted() {
    this.generateTubes();
  },
};
</script>

<style scoped lang="less">
.game {
  text-align: center;

  &__test-tubes {
    display: flex;
    justify-content: center;
    gap: 1em;
  }

  &__controls {
    margin-top: 2em;
  }

  &__reset-button {
    padding: 1em 2em;
    cursor: pointer;
  }
}
</style>
