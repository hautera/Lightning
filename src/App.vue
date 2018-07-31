<template>
  <div id="app">
     <ul v-if="response.length != 0">
        <li v-for="strike in response" :key="strike.id">
           <span>
             <img src="./assets/images.png" /><h3>Distance:{{strike.relativeTo.distanceMI}} miles from {{location}}</h3><h4>{{Math.abs(strike.ob.pulse.peakamp)}} amps</h4>
             <p>
               {{strike.ob.age}} seconds ago
             </p>
          </span>
        </li>
     </ul>
     <div v-else>
        <h1>No new lightning strikes in the area!</h1>
     </div>
  </div>
</template>

<script>
// var lat = 48.277271
// var log =  -122.667852

export default {
  name: 'App',
  /**
   * Declares the parameters of the Vue App
   * @returns a JSON object with two properties:
   * response: an array of recent lightning strikes in the aeris api format
   * location: the location of the request, currently hard-coded as Andrews, Tx
   */
  data: function () {
    return {
      response: [],
      location: 'Andrews, Texas'
    }
  },

  /**
   * Sets a 30s interval to refresh data, and a 1s interval to update age of the lightning strikes
   */
  created: function () {
    // update the lightning chart every 30 seconds
    window.setInterval(this.fetchData, 30000)
    window.setInterval(this.incrementSeconds, 1000)
  },

  methods: {
    /**
     * fetches and parses data
     */
    fetchData: function () {
      fetch(`https://api.aerisapi.com/lightning?p=Andrews,tx&client_id=EHFH6G7xUCozKVwS96iBf&client_secret=WiQfwsOzCHBqILNWfanuJDFBbOiNiuwTL22yOjiq`)
        .then(response => response.json())
        .then(data => {
          this.numStrikes = data.response.length
          this.response = data.response
        })
    },

    /**
     * Increments the age of each lightning strike by 1s 
     */
    incrementSeconds: function () {
      for (let i = 0; i < this.response.length; i++) {
        this.response[i].ob.age += 1
      }
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
