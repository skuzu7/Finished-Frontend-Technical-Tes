<template>
  <div class="min-h-screen bg-gray-900 text-white p-4">
    <header class="text-center">
      <h1 class="text-4xl font-bold mb-6">Cryptocurrency Prices</h1>
    </header>
    <PriceDisplay />
    <HistoricalPrice />
  </div>
</template>

<script>
import PriceDisplay from './components/PriceDisplay.vue'
import HistoricalPrice from './components/HistoricalPrice.vue'

export default {
  name: 'App',
  components: {
    PriceDisplay,
    HistoricalPrice
  }
}
</script>
