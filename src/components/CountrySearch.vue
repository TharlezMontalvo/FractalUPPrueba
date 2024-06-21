<template>
  <div class="container mt-3">
    <div class="input-group mb-3 search-bar" @click="openFilter">
      <label for="countryInput" class="input-label">País</label>
      <input
        id="countryInput"
        v-model="searchTerm"
        class="form-control"
        type="text"
        placeholder="Escribe el país que deseas ver"
      />
      <button @click="onSearch" class="btn btn-primary">
        <i class="bi bi-search"></i> Buscar
      </button>
    </div>

    <div class="continent-filter-container">
      <continent-filter
        v-if="showFilter"
        :continents="continents"
        @close="showFilter = false"
        @filter="filterByContinent"
      />
    </div>

    <div v-if="loading" class="spinner-border" role="status">
      <span class="visually-hidden">Cargando...</span>
    </div>

    <div v-if="error" class="alert alert-danger" role="alert">
      Error: {{ error.message }}
    </div>

    <div class="row">
      <div
        v-for="country in filteredCountries"
        :key="country.code"
        class="col-md-4 mb-4"
      >
        <div
          class="card"
          @click="loadCountryDetails(country)"
          :class="{ 'selected-country': selectedCountry && selectedCountry.code === country.code }"
        >
          <div
            class="card-img-top"
            :style="{ backgroundImage: 'url(' + getImageUrl(country) + ')' }"
          >
          </div>
          <div
            class="card-body"
            :class="{ 'selected-country-body': selectedCountry && selectedCountry.code === country.code }"
          >
            <div class="row align-items-center">
              <div class="col-3">
                <img :src="getFlagUrl(country.code)" class="img-fluid" alt="Bandera del país">
              </div>
              <div class="col-9">
                <h5 class="card-title mb-0 country-name"
                    :class="{ 'text-white': selectedCountry && selectedCountry.code === country.code }">
                  {{ country.name }}
                </h5>
                <p class="card-text"
                   :class="{ 'text-white': selectedCountry && selectedCountry.code === country.code }">
                  {{ country.continent.name }}
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div v-if="selectedCountry" class="card country-details">
      <button @click="selectedCountry = null" class="close-btn">×</button>
      <div class="card-img-top"
           :style="{ backgroundImage: 'url(' + selectedCountryImage + ')' }"
      ></div>
      <div class="card-body">
        <div class="row align-items-center">
          <div class="col-3">
            <img :src="getFlagUrl(selectedCountry.code)" class="img-fluid" alt="Bandera del país">
          </div>
          <div class="col-9">
            <h5 class="card-title mb-0 country-name">{{ selectedCountry.name }}</h5>
            <p class="card-text">{{ selectedCountry.continent.name }}</p>
          </div>
        </div>
        <p class="card-text"><span class="detail-title">Capital:</span> {{ selectedCountry.capital }}</p>
        <p class="card-text"><span class="detail-title">Idiomas:</span>
          <span v-if="selectedCountry.languages.length > 1">
            {{ selectedCountry.languages.map(language => language.name).join(', ') }}
          </span>
          <span v-else>
            {{ selectedCountry.languages[0].name }}
          </span>
        </p>
        <p class="card-text"><span class="detail-title">Población:</span> {{ formatPopulation(selectedCountry.population) }}</p>
        <p class="card-text" v-if="selectedCountry.currencies.length > 0"><span class="detail-title">Moneda:</span>
          <span v-for="(currency, index) in selectedCountry.currencies" :key="index">{{ currency.name }}</span>
        </p>
        <div class="scrollable-region" v-if="selectedCountry.region">
          <p class="card-text"><span class="detail-title">Región:</span> {{ selectedCountry.region }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue';
import axios from 'axios';
import { useQuery } from '@vue/apollo-composable';
import gql from 'graphql-tag';
import ContinentFilter from './ContinentFilter.vue';

const GET_COUNTRIES = gql`
  query GetCountries {
    countries {
      code
      name
      capital
      languages {
        name
      }
      continent {
        name
      }
    }
  }
`;

const ACCESS_KEY = 'cyqgDXtZSx0-bgWxPE1DVQz0RIztznYFvcfA4e5fbVw'; // Tu Access Key de Unsplash
const UNSPLASH_API_URL = 'https://api.unsplash.com';

const CONTINENTS = [
  { name: 'Africa', imageQuery: 'Africa' },
  { name: 'Asia', imageQuery: 'Asia' },
  { name: 'Europe', imageQuery: 'Europe' },
  { name: 'America', imageQuery: 'America' },
  { name: 'Oceania', imageQuery: 'Oceania' },
];

export default {
  name: 'CountrySearch',
  components: {
    ContinentFilter,
  },
  setup() {
    const searchTerm = ref('');
    const { result, loading, error } = useQuery(GET_COUNTRIES);
    const imageCache = ref({});
    const selectedCountry = ref(null);
    const showFilter = ref(false);
    const continents = ref([]);

    const selectedCountryImage = computed(() => {
      return selectedCountry.value ? imageCache.value[selectedCountry.value.name] : '';
    });

    const filteredCountries = computed(() => {
      if (!result.value) return [];
      return result.value.countries.filter((country) =>
        country.name.toLowerCase().includes(searchTerm.value.toLowerCase())
      );
    });

    const loadImages = async () => {
      try {
        for (const country of filteredCountries.value) {
          if (!imageCache.value[country.name]) {
            const query = encodeURIComponent(`${country.name} landscape`); // Agregamos 'landscape' para buscar paisajes
            const url = `${UNSPLASH_API_URL}/photos/random?query=${query}&orientation=landscape&client_id=${ACCESS_KEY}`;

            const response = await axios.get(url);
            if (response.data.urls && response.data.urls.regular) {
              imageCache.value[country.name] = response.data.urls.regular;
            } else {
              imageCache.value[country.name] = 'default-image-url.jpg';
            }
            console.log(`Imagen cargada para ${country.name}: ${imageCache.value[country.name]}`);
          }
        }
      } catch (error) {
        console.error('Error al cargar imágenes desde Unsplash:', error);
        for (const country of filteredCountries.value) {
          imageCache.value[country.name] = 'default-image-url.jpg';
        }
      }
    };

    const loadContinentImages = async () => {
      try {
        for (const continent of CONTINENTS) {
          const url = `${UNSPLASH_API_URL}/photos/random?query=${continent.imageQuery}&orientation=landscape&client_id=${ACCESS_KEY}`;

          const response = await axios.get(url);
          if (response.data.urls && response.data.urls.regular) {
            continents.value.push({ name: continent.name, image: response.data.urls.regular });
          } else {
            continents.value.push({ name: continent.name, image: 'default-image-url.jpg' });
          }
        }
      } catch (error) {
        console.error('Error al cargar imágenes de continentes desde Unsplash:', error);
        for (const continent of CONTINENTS) {
          continents.value.push({ name: continent.name, image: 'default-image-url.jpg' });
        }
      }
    };

    const getFlagUrl = (countryCode) => {
      return `https://flagcdn.com/w320/${countryCode.toLowerCase()}.png`;
    };

    const onSearch = () => {
      loadImages();
    };

    const loadCountryDetails = async (country) => {
      try {
        const response = await axios.get(`https://restcountries.com/v3.1/name/${country.name}`);
        if (response.data.length > 0) {
          const countryDetails = response.data[0];
          selectedCountry.value = {
            ...country,
            population: countryDetails.population,
            currencies: countryDetails.currencies,
            region: countryDetails.region,
          };
        } else {
          console.error(`No se encontraron detalles para ${country.name}`);
        }
      } catch (error) {
        console.error(`Error al cargar detalles para ${country.name}:`, error);
      }
    };

    const formatPopulation = (population) => {
      if (population >= 1000000) {
        return `${(population / 1000000).toFixed(1)}M people`;
      } else if (population >= 1000) {
        return `${(population / 1000).toFixed(0)}K people`;
      } else {
        return `${population} people`;
      }
    };

    const openFilter = () => {
      showFilter.value = true;
    };

    const filterByContinent = (continent) => {
      searchTerm.value = continent.name;
      showFilter.value = false;
      loadImages();
    };

    onMounted(() => {
      loadImages();
      loadContinentImages();
    });

    const getImageUrl = (country) => {
      return imageCache.value[country.name] || 'default-image-url.jpg';
    };

    return {
      searchTerm,
      loading,
      error,
      filteredCountries,
      getFlagUrl,
      imageCache,
      onSearch,
      loadCountryDetails,
      selectedCountry,
      selectedCountryImage,
      formatPopulation,
      showFilter,
      continents,
      openFilter,
      filterByContinent,
      getImageUrl,
    };
  },
};
</script>

<style scoped>
.container {
  padding: 15px;
}

.card-img-top {
  object-fit: cover;
  height: 200px;
}

.card-body .row {
  display: flex;
  align-items: center;
}

.card-body .row .col-3 img {
  max-height: 50px;
}

.country-name {
  color: #00BFFF;
  font-weight: bold;
}

.btn-primary {
  background-color: #00BFFF;
  border-color: #00BFFF;
  border-radius: 50px;
  margin-left: 5px;
}

.input-group .form-control {
  border-radius: 50px;
}

.search-bar {
  display: flex;
  align-items: center;
  position: relative;
}

.input-label {
  position: absolute;
  top: 0;
  left: 10px;
  background-color: white;
  padding: 0 5px;
  margin-top: -10px;
  font-weight: bold;
  color: #00BFFF;
  z-index: 1;
}

.country-details {
  position: fixed;
  bottom: 0;
  right: 0;
  width: 300px;
  height: 75%;
  background-color: white;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  overflow-y: auto;
  padding: 20px;
  z-index: 1000;
  display: flex;
  flex-direction: column;
}

.close-btn {
  position: absolute;
  top: 10px;
  right: 10px;
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
}

.scrollable-region {
  max-height: 150px;
  overflow-y: auto;
}

.detail-title {
  font-weight: bold;
  color: #00BFFF;
}

.selected-country-body {
  background-color: #00BFFF;
}

.selected-country-body .country-name.text-white,
.selected-country-body .card-text.text-white {
  color: white;
}

.bottom-highlight {
  height: 10px;
}

.continent-filter-container {
  position: absolute;
  width: 100%;
  top: 100%;
  left: 0;
  z-index: 100;
}

@media (max-width: 768px) {
  .card-img-top {
    height: 150px; 
  }

  .country-details {
    width: 100%;
    height: 100%;
    bottom: 0;
    left: 0;
    margin: 0;
  }

  .close-btn {
    top: 5px; 
    right: 5px;
  }

  .card-body .row .col-3 img {
    max-height: 40px;
  }

  .continent-filter-container {
    position: relative;
    top: auto;
    left: auto;
  }
}
</style>
