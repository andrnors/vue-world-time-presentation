<template>
  <div class="locations">
    <!-- <h1>Locations</h1> -->

    <!-- <input class="search" v-model="search" type="text" placeholder="Enter a city" /> -->

    <!-- 
    <div class="locationsList">
      <loader v-if="loading" />
      <div
        class="location"
        v-for="(location, index) in locations"
        :key="index"
        @click="updateLocation(location)"
      >{{ location | formatLocations }}</div>
    </div>-->
  </div>
</template>

<script>
// // // import Loader from "@/components/Loader.vue";

export default {
  name: "Locations",
  // components: { Loader },
  /** Local state for component */
  data() {
    return {
      locations: [],
      search: "",
      loading: false
    };
  },
  // Runs whenever the component is loaded
  async mounted() {
    this.loading = true;
    await this.getLocations();
    this.loading = false;
  },
  filters: {
    /**  Used to format how the location looks in the UI */
    formatLocations(location) {
      return location.split("/").join(" - ");
    }
  },
  computed: {
    /**  Used to filter out the locations matching the search */
    filteredLocations() {
      return this.locations.filter(location =>
        location.toLowerCase().includes(this.search.toLowerCase())
      );
    }
  },
  methods: {
    /** Fetches the all locations/timezones */
    async getLocations() {
      const locations = await this.$http.get(
        "http://worldtimeapi.org/api/timezone"
      );
      this.locations = locations.data;
    },
    /** Passes the location/timezone to the home pages */
    updateLocation(location) {
      this.$router.push({ name: "Home", params: { location: location } });
    }
  }
};
</script>

<style scoped>
.locations {
  /* margin: 2rem; */
  display: flex;
  flex-direction: column;
  align-items: center;
}
.search {
  margin: 1rem auto;
  height: 1.5rem;
  width: 25rem;
  max-width: 80%;
  border-radius: 24px;
  padding: 8px;
  font-size: 20px;
  outline: 0;
}

.locationsList {
  width: 45rem;
  max-width: 90%;
}

.location {
  padding: 8px;
  margin: 16px;
  border-radius: 24px;
  border: 1px solid darkred;
  font-size: 1.125rem;
}
</style>
