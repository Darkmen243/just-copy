<template>
  <div class="map-modal">
    <div class="map-modal__background" 
      @click="close">
    </div>
    <div class="map-modal__content">
      <button 
        class="map-modal__close" 
        @click="close">
        Закрыть
      </button>
      <h3>Выберите координаты на карте</h3>
        <div ref="map" class="map-modal__map"></div>
      <button 
        class="map-modal__select" 
        @click="confirmSelection">
        Выбрать
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "MapModal",
  props: {
    defaultCoordinates: {
      type: String,
      default: "30, 30"
    },
  },
  data() {
    return {
      map: null,
      placemark: null
    };
  },
  mounted() {
    this.initMap();
  },
  methods: {
    initMap() {
      const [lat, lng] = this.defaultCoordinates.split(",").map(Number);
      ymaps.ready(() => {
        this.map = new ymaps.Map(this.$refs.map, {
          center: [lat, lng],
          zoom: 10,
        });

        this.placemark = new ymaps.Placemark([lat, lng], {}, { draggable: true });
        this.map.geoObjects.add(this.placemark);
      });
    },
    confirmSelection() {
      const coords = this.placemark.geometry.getCoordinates();
      this.$emit("select", coords.join(","));
    },
    close() {
      this.$emit("close");
    },
  },
};
</script>

<style scoped lang="less">
.map-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.map-modal__background {
  position: absolute;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
}

.map-modal__content {
  position: relative;
  background: white;
  border-radius: 8px;
  width: 40em;
  overflow: hidden;
}

.map-modal__close {
  font-weight: bold;
  color: red;
  cursor: pointer;
  margin-top: 1em;
}

.map-modal__map {
  width: 100%;
  height: 30em;
}

.map-modal__select {
  padding: 1em;
  background-color: blue;
  color: white;
  border-radius: 4px;
  cursor: pointer;
  margin: 1.5em;
}
</style>
