<template>
  <div id="app">
    <div class="container">
      <nav class="navbar navbar-light bg-light">
        <a href="http://www.jasont.dev/" class="navbar-brand">jasont.dev</a>
      </nav>

      <div v-if="remainingDaysOff.length > 0">
        <h1>day off dashboard</h1>

        <div class="jumbotron">
          <h1 class="display-4">{{ numDaysToNextDayOff }} days</h1>
          <p>until your next day off</p>
        </div>

        <div class="row">
          <div class="col-md-6 col-xs-12">
            <div class="card">
              <div class="card-body">
                <h3 class="card-title">next day off</h3>
                {{ remainingDaysOff[0].toDateString() }}
              </div>
            </div>
          </div>

          <div class="col-md-6 col-xs-12">
            <div class="card">
              <div class="card-body">
                <h3 class="card-title">previous day off</h3>
                {{ previousDaysOff.slice(-1).pop().toDateString() }}
              </div>
            </div>
          </div>

        </div>

        <div v-if="remainingDaysOff.length > 1">
          <h2 class="my-3">remaining days off</h2>
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
    },
    previousDaysOff() {
      return this.allDaysOff.filter(dayOff => dayOff < Date.now());
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