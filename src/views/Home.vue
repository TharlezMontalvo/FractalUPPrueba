<template>
  <div class="container mt-3">
    <CountrySearch />
  </div>
</template>

<script>
import CountrySearch from '../components/CountrySearch.vue';

export default {
  name: 'HomePage',
  components: {
    CountrySearch,
  },
};
</script>
