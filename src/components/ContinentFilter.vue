<template>
  <div class="continent-filter" :class="{ 'show': showFilter }">
    <div class="filter-header">
      <h5>Filtrar por Continente</h5>
      <button @click="closeFilter" class="close-btn">×</button>
    </div>
    <div class="filter-options">
      <div v-for="(row, index) in Math.ceil(continents.length / 3)" :key="index" class="continent-row">
        <div v-for="(continent, rowIndex) in continents.slice(index * 3, (index + 1) * 3)" :key="rowIndex" class="continent-option" @click="filterByContinent(continent)">
          <img :src="continent.image" alt="Imagen del continente" class="continent-image"/>
          <p>{{ continent.name }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ContinentFilter',
  props: {
    continents: Array,
    showFilter: Boolean,
  },
  methods: {
    closeFilter() {
      this.$emit('close');
    },
    filterByContinent(continent) {
      this.$emit('filter', continent); 
    },
  },
};
</script>

<style scoped>
.continent-filter {
  position: fixed;
  top: 56%;
  left: 30%;
  width: 100%;
  max-width: 700px;
  background-color: white;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  z-index: 1000;
  padding: 10px;
  border-radius: 10px;
  transform: translateY(-100%);
  transition: transform 0.3s ease;
}

.filter-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.close-btn {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
}

.filter-options {
  display: flex;
  flex-wrap: wrap;
}

.continent-row {
  display: flex;
  width: 100%;
  justify-content: space-between;
  margin-bottom: 10px;
}

.continent-option {
  flex: 0 1 calc(33.33% - 20px);
  margin: 10px;
  text-align: center;
  cursor: pointer;
}

.continent-image {
  width: 100%;
  height: 100px;
  object-fit: cover;
  border-radius: 5px;
}

.continent-option p {
  margin-top: 5px;
  font-weight: bold;
  color: #00BFFF;
}

.show {
  transform: translateY(0);
}
</style>
