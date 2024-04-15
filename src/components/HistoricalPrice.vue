<template>
  <div class="max-w-lg mx-auto bg-gray-800 rounded-lg p-6">
    <h2 class="text-xl font-semibold mb-4">Check Historical Price</h2>
    <form @submit.prevent="fetchHistoricalPrice" class="animate-fadeIn">
      <div class="mb-4">
        <label for="coin" class="block text-sm font-medium text-gray-400">Coin Name</label>
        <select id="coin" v-model="coin" class="mt-1 block w-full p-2 bg-gray-700 border-gray-600 rounded-md" aria-required="true">
          <option value="">Select a coin</option>
          <option value="bitcoin">Bitcoin</option>
          <option value="dacxi">Dacxi</option>
          <option value="ethereum">Ethereum</option>
          <option value="cosmos">Cosmos</option>
        </select>
      </div>
      <div class="mb-4">
        <label for="date" class="block text-sm font-medium text-gray-400">Date</label>
        <input type="date" id="date" v-model="date" class="mt-1 block w-full p-2 bg-gray-700 border-gray-600 rounded-md" aria-describedby="dateHelp" aria-required="true">
        <p id="dateHelp" class="text-gray-400 text-sm mt-1">Format: YYYY-MM-DD</p>
        <p v-if="dateError" class="text-red-500 text-sm mt-1">{{ dateError }}</p>
      </div>
      <button type="submit" class="w-full p-2 bg-blue-500 hover:bg-blue-700 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-600">Get Price</button>
    </form>
    <div v-if="loading" class="text-center mt-4">
      <div class="loader ease-linear rounded-full border-4 border-t-4 border-gray-200 h-12 w-12 mb-4 mx-auto" role="status" aria-label="Loading"></div>
      <p class="text-white">Loading...</p>
    </div>
    <p v-if="historicalPrice" class="mt-4 text-2xl">{{ formatCurrency(historicalPrice) }}</p>
    <p v-else-if="error" class="mt-4 text-xl text-red-500">{{ error }}</p>
  </div>
</template>

<script>
import axios from 'axios';
import { API_BASE_URL } from '../@constants/constants';

export default {
  name: 'HistoricalPriceTextInput',
  data() {
    return {
      coin: '',
      date: '',
      historicalPrice: null,
      loading: false,
      error: '',
      dateError: ''
    };
  },
  methods: {
    formatCurrency(value) {
      if (!value) return '$0.00';
      return `$${value.toFixed(2)}`;
    },
    async fetchHistoricalPrice() {
      if (!this.date) {
        this.dateError = 'Please enter a valid date.';
        return;
      }

      if (!this.coin) {
        this.error = 'Please select a valid coin.';
        return;
      }

      const currentDate = new Date();
      const selectedDate = new Date(this.date);
      if (selectedDate > currentDate) {
        this.dateError = 'Please select a date in the past.';
        return;
      }

      this.loading = true;
      this.error = '';
      this.dateError = '';

      const url = `${API_BASE_URL}/simple/price?ids=${this.coin}&vs_currencies=usd`;

      try {
        const response = await axios.get(url);
        if (response.data && response.data[this.coin] && response.data[this.coin].usd) {
          this.historicalPrice = response.data[this.coin].usd;
        } else {
          this.error = 'No data available for the selected date or coin.';
          this.historicalPrice = null;
        }
      } catch (error) {
        console.error('Error fetching historical price:', error);
        this.error = 'Failed to fetch historical price. Please try again later.';
        this.historicalPrice = null;
      }

      this.loading = false;
    }
  }
}
</script>

<style scoped>
.animate-fadeIn {
  animation: fadeIn 0.5s ease-in-out;
}

.loader {
  border-top-color: #3498db;
  animation: spin 1s ease-in-out infinite;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>
