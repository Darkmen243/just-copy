<template>
  <div class="coordinate-input">
    <input
      v-model="localCoordinates"
      class="coordinate-input__field"
      type="text"
      placeholder="Введите координаты через запятую"
    />
    <button 
      class="coordinate-input__button" 
      @click="() => handleButtonClick()">
      Указать
    </button>
    <MapModal
      v-if="isModalOpen"
      :default-coordinates="currentCoordinates"
      @close="() => closeModal()"
      @select="(coords) => setCoordinates(coords)"
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
  props: {
    value: {
      type: String,
      default: ""
    },
  },
  data() {
    return {
      currentCoordinates: "", 
      isModalOpen: false
    };
  },
  computed: {
    localCoordinates: {
      get() {
        return this.value; 
      },
      set(value) {
        this.$emit("input", value); 
      },
    },
  },
  methods: {
    handleButtonClick() {
      if (!this.localCoordinates) {
        this.getCurrentLocation();
      } else {
        this.currentCoordinates = this.localCoordinates;
        this.openModal();
      }
    },
    getCurrentLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            const { latitude, longitude } = position.coords;
            this.currentCoordinates = `${latitude.toFixed(6)},${longitude.toFixed(6)}`;
            this.localCoordinates = this.currentCoordinates; 
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
      this.localCoordinates = newCoordinates; 
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
