<template>
  <div class="container mx-auto px-4">


    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-4 mb-8">
      <div v-for="(price, coin) in prices" :key="coin" class="bg-gray-800 rounded-lg shadow-md animate-fadeIn p-6">
        <p class="text-lg font-semibold">{{ coin.toUpperCase() }}</p>
        <p class="text-3xl">{{ formatCurrency(price.usd) }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import { API_BASE_URL } from '../@constants/constants';

export default {
  name: 'PriceDisplay',
  data() {
    return {
      prices: {},
      error: ''
    };
  },
  created() {
    this.fetchPrices();
    setInterval(this.fetchPrices, 10000);
  },
  methods: {
    formatCurrency(value) {
      if (!value) return '$0.00';
      return `$${value.toFixed(2)}`;
    },
    async fetchPrices() {
      try {
        const response = await axios.get(`${API_BASE_URL}/simple/price?ids=bitcoin,dacxi,ethereum,cosmos&vs_currencies=usd`);
        this.prices = response.data;
      } catch (error) {
        console.error('Error fetching prices:', error);
        this.error = 'Failed to fetch prices. Please try again later.';
      }
    }
  }
}
</script>

<style scoped>
.animate-fadeIn {
  animation: fadeIn 0.5s ease-in-out;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
</style>
