<template>
  <main v-if="!loading">
    <DataTitle :title="title" :dataDate="dataDate" />

    <DataBoxes :stats="stats"/>

    <CountrySelect @get-country="getCountryData" :countries="countries" />

    <button @click="completeTracking" v-if="stats.Country" class="bg-green-700 text-white rounded p-3 focus:outline-none hover:bg-green-600 mb-10">{{ title }} Tracking Complete</button>
  </main>

  <main class="flex flex-col align-center justify-center text-center" v-else>
    <div class="text-gray-500 text-3xl mt-32 mb-6">
      Fetching data...
    </div>
    <img :src="loadingImage" class="w-24 m-auto" alt="">
  </main>
</template>

<script>
import DataTitle from '@/components/DataTitle'
import DataBoxes from '@/components/DataBoxes'
import CountrySelect from '@/components/CountrySelect'

export default {
  name: 'HomeView',
  components: {
    DataTitle,
    DataBoxes,
    CountrySelect,
  },
  data () {
    return {
      loading: true,
      title: 'Global',
      dataDate: '',
      stats: {},
      countries: [],
      loadingImage: require('../assets/loading.gif')
    }
  },
  methods: {
    async fetchCovidData() {
      const response = await fetch('https://api.covid19api.com/summary')
      const data = response.json()
      return data
    },
    getCountryData(country) {
      this.stats = country,
      this.title = country.Country
    },
    async completeTracking() {
      this.loading = true
      const data = await this.fetchCovidData()
      this.title = 'Global'
      this.stats = data.Global
      this.loading = false
    }
  },
  async created() {
    const data = await this.fetchCovidData()
    
    this.dataDate = data.Date
    this.stats = data.Global
    this.countries = data.Countries
    this.loading = false
  },
}
</script>
