<template>
  <div class="home-page">
    <h1>Введите координаты</h1>
    <CoordinateInput 
      v-model="coordinates" 
    />
    <h1>Координаты: {{ coordinates }}</h1>
  </div>
</template>

<script>
import CoordinateInput from "../../../components/coordinateInput/CoordinateInput.vue";

export default {
  name: "HomePage",
  components: {
    CoordinateInput
  },
  data() {
    return {
      coordinates: ""
    };
  },
};
</script>

<style scoped lang="less">
.home-page {
  text-align: center;
  padding: 1em;
}

h1 {
  margin-bottom: 2em;
  font-size: 2em;
  color: black;
}
</style>
