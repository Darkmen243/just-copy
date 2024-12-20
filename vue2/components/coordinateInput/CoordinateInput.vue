<template>
  <div class="coordinate-input">
    <input
      v-model="coordinates"
      class="coordinate-input__field"
      type="text"
      placeholder="Введите координаты через запятую"
    />
    <button 
      class="coordinate-input__button" 
      @click="handleButtonClick">
      Указать
    </button>
    <MapModal
      v-if="isModalOpen"
      :default-coordinates="currentCoordinates"
      @close="closeModal"
      @select="setCoordinates"
    />
  </div>
</template>

<script>
import MapModal from "../mapModal/MapModal.vue";

export default {
  name: "CoordinateInput",
  components: {
    MapModal
  },
  data() {
    return {
      coordinates: "",
      currentCoordinates: "",
      isModalOpen: false
    };
  },
  methods: {
    handleButtonClick() {
      if (!this.coordinates.trim()) {
        this.getCurrentLocation();
      } else {
        this.currentCoordinates = this.coordinates;
        this.openModal();
      }
    },
    getCurrentLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            const { latitude, longitude } = position.coords;
            this.currentCoordinates = `${latitude.toFixed(6)},${longitude.toFixed(6)}`;
            this.openModal();
          },
          (error) => {
            console.error("Ошибка при получении геопозиции: ", error.message);
            alert("Не удалось получить геопозицию.");
          }
        );
      } else {
        alert("Ваш браузер не поддерживает геолокацию.");
      }
    },
    openModal() {
      this.isModalOpen = true;
    },
    closeModal() {
      this.isModalOpen = false;
    },
    setCoordinates(newCoordinates) {
      this.coordinates = newCoordinates;
      this.closeModal();
    },
  },
};
</script>

<style scoped lang="less">
.coordinate-input {
  display: flex;
  align-items: center;

  &__field {
    flex: 1;
    padding: 0.5em;
    border: 1px solid lightgrey;
    border-radius: 0.5em;
  }

  &__button {
    margin-left: 0.5em;
    padding: 0.5em;
    background-color: lightblue;
    color: white;
    border: none;
    border-radius: 0.5em;
    cursor: pointer;

    &:hover {
      background-color: blue;
    }
  }
}
</style>
