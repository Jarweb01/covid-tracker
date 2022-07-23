<template>
  <main v-if="!loading">
    <data-title :text="title" :dataDate="dataDate"></data-title>
    <data-boxes :stats="stats"></data-boxes>
    <country-select
      :countries="countries"
      @get-country="getCountryData"
    ></country-select>

    <button
      class="bg-green-700 text-white rounded p-3 mt-10 focus:outline-none hover:bg-green-600"
      v-if="stats.Country"
      @click="clearCountryData"
    >
      Clear Country
    </button>
  </main>

  <main class="flex flex-col align-center justify-center text-center" v-else>
    <div class="text-gray-500 text-3xl mt-10 mb-6">Fetching Data</div>
    <img class="w-24 m-auto" :src="loadingImage" alt="" />
  </main>
</template>

<script>
// @ is an alias to /src
import DataTitle from "../components/DataTitle";
import DataBoxes from "../components/DataBoxes";
import CountrySelect from "../components/CountrySelect";

export default {
  name: "HomeView",
  components: {
    DataTitle,
    DataBoxes,
    CountrySelect,
  },
  data() {
    return {
      loading: true,
      title: "Global",
      dataDate: "",
      stats: {},
      countries: [],
      loadingImage: require("../assets/hourglass.gif"),
    };
  },
  async created() {
    const data = await this.fetchCovidData();

    this.dataDate = data.Date;
    this.stats = data.Global;
    this.countries = data.Countries;
    this.loading = false;
  },
  methods: {
    async fetchCovidData() {
      const res = await fetch("https://api.covid19api.com/summary");
      const data = await res.json();
      return data;
    },
    getCountryData(country) {
      if (typeof country === "undefined") {
        this.clearCountryData();
        return;
      }
      console.log(country);
      this.stats = country;
      this.title = country.Country;
    },
    async clearCountryData() {
      this.loading = true;
      const data = await this.fetchCovidData();
      this.title = "Global";
      this.stats = data.Global;
      this.loading = false;
    },
  },
};
</script>
