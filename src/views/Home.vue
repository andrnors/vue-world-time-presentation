<template>
  <!-- <loader v-if="loading" /> -->
  <div class="container">
    <!-- <h1>{{ location.datetime | timeFormater }}</!-->

    <!-- <p class="day">{{ location | dayFormater }}</p> -->

    <!-- <div class="timezone">{{ location.timezone | timezoneFormater }}</div> -->

    <!-- <router-link class="button" to="/locations">+</router-link> -->
  </div>
</template>
<script>
// // import Loader from "@/components/Loader.vue";
export default {
  name: "Home",
  // components: { Loader },
  /** Local state of the component */
  data() {
    return {
      locationEndpoint: "Europe/Oslo",
      location: {},
      loading: false,
      interval: null
    };
  },
  // Filters used to format the different UI components
  filters: {
    timeFormater(datetime) {
      if (datetime) {
        const date = datetime.split("T")[1].split(":");
        return `${date[0]}:${date[1]}.${date[2].substring(0, 2)}`;
      }
    },
    timezoneFormater(timezone) {
      return timezone ? timezone.replace("/", " - ") : "";
    },
    dayFormater(location) {
      const weekdays = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday"
      ];
      if (location.datetime) {
        const date = location.datetime
          .split("T")[0]
          .split("-")
          .reverse()
          .join(".");
        const day = weekdays[location.day_of_week];
        return `${day}, ${date}`;
      }
    }
  },
  /** Runs every time the component is mounted */
  async mounted() {
    this.loading = true;
    if (this.$route.params.location) {
      this.locationEndpoint = this.$route.params.location;
    }
    await this.getLocation(this.locationEndpoint);
    this.loading = false;
  },
  /** Runs when the component is created */
  created() {
    this.interval = setInterval(
      async () => await this.getLocation(this.locationEndpoint),
      5000
    );
  },
  /** Runs when the component is destroyed */
  destroyed() {
    clearInterval(this.interval);
  },
  /** Property that watch changes props */
  watch: {
    async $route(to) {
      await this.getLocation(to.params.location);
    }
  },
  methods: {
    /** Fetches the current location */
    async getLocation(location) {
      this.location = (
        await this.$http.get(`http://worldtimeapi.org/api/timezone/${location}`)
      ).data;
    }
  }
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: 10rem auto;
}

h1 {
  font-size: 3.5rem;
}
.timezone {
  font-size: 1.5rem;
}

.day {
  font-size: 2rem;
}

.button {
  background: darkred;
  color: white;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  font-size: 1.5rem;
  margin: 4rem 0 0 0;
  text-decoration: none;
  line-height: 3rem;
}
</style>
