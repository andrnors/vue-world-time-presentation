<template>
  <loader v-if="loading" />
  <div v-else class="container">
    <h1>{{ location.datetime | timeFormater }}</h1>
    <p class="day">{{ location | dayFormater }}</p>
    <div class="timezone">{{ location.timezone | timezoneFormater }}</div>

    <router-link class="button" to="/locations">+</router-link>
  </div>
</template>
<script>
import Loader from "@/components/Loader.vue";
export default {
  name: "Home",
  components: { Loader },
  data() {
    return {
      locationEndpoint: "Europe/Oslo",
      location: {},
      loading: false,
      interval: null
    };
  },
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
        "Wednesdag",
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
  async mounted() {
    this.loading = true;
    if (this.$route.params.location) {
      this.locationEndpoint = this.$route.params.location;
    }
    await this.getLocation(this.locationEndpoint);
    this.loading = false;
  },
  created() {
    this.interval = setInterval(
      async () => await this.getLocation(this.locationEndpoint),
      5000
    );
  },
  destroyed() {
    clearInterval(this.interval);
  },
  watch: {
    async $route(to) {
      await this.getLocation(to.params.location);
    }
  },
  methods: {
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
