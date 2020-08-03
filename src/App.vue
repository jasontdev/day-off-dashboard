<template>
  <div id="app">
    <div class="container">
      <h1>day-off dashboard</h1>
      <div v-if="remainingDaysOff.length > 0">
        <h3>next day-off</h3>
          {{ remainingDaysOff[0].toDateString() }} in {{ numDaysToNextDayOff }} days.
        <div v-if="remainingDaysOff.length > 1"><h3>remaining days-off</h3>
          <table class="table table-striped">
            <tbody>
            <tr v-for="dayOff in remainingDaysOff.slice(1)" :key="dayOff.toString()">
              <td>{{ dayOff.toDateString() }}</td>
            </tr>
            </tbody>
          </table>
        </div>
      </div>
      <div v-else>
        <p>Sorry, you don't have any days-off remaining.</p>
      </div>
    </div>
  </div>
</template>

<script>
import 'bootstrap';
import 'bootstrap/dist/css/bootstrap.min.css'

export default {
  name: 'App',
  data() {
    return {
      daysOffFromApi: [],
    }
  },
  mounted() {
    this.fetchDaysOffFromApi();
  },
  computed: {
    allDaysOff() {
      return this.daysOffFromApi.map(dayOff => new Date(Date.parse(dayOff.date)));
    },
    apiUri() {
      return process.env.VUE_APP_API_URI;
    },
    remainingDaysOff() {
      return this.allDaysOff.filter(dayOff => dayOff > Date.now())
    },
    numDaysToNextDayOff() {
      return Math.floor((this.remainingDaysOff[0] - Date.now()) / 86400000);
    }
  },
  methods: {
    fetchDaysOffFromApi() {
      fetch(this.apiUri)
          .then(response => response.json())
          .then(response => this.daysOffFromApi = response.dates)
    },
  }
}
</script>