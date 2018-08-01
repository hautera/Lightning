<template>
  <div id="app" v-on:keyup.enter="fetchData">
		<input v-model="userInput" />
		<input type='button'  value="GO" v-on:click="fetchData"/>
		<div v-if="success">
     <table v-if="response.length != 0">
			 <tr>
					<th class="gray-border"><img src="./assets/images.png" /> </th>
					<th class="gray-border">Distance from {{location}}</th>
					<th class="gray-border">Peak Amperage</th>
					<th class="gray-border">Seconds Ago</th>
			 </tr>
      <tr v-for="strike in response" :key="strike.id" class="box">
			 <td class="gray-border"><img src="./assets/images.png" /> </td>
			 <td class="gray-border">{{strike.relativeTo.distanceMI}} mi </td>
			 <td class="gray-border">{{Math.abs(strike.ob.pulse.peakamp)}} amps</td>
			 <td class="gray-border">{{strike.ob.age}} s </td>
      </tr>
     </table>
     <div v-else>
        <h1>No new lightning strikes in the area!</h1>
     </div>
  </div>
	<div v-else>
		{{error}}
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
			success: false,
      response: [],
      location: 'Andrews, Texas',
			error: '',
			userInput: ''
    }
  },

  /**
   * Sets a 30s interval to refresh data, and a 1s interval to update age of the
	 * lightning strikes
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
			if(this.userInput !== ''){
				this.location = this.userInput
			}
      fetch(`https://api.aerisapi.com/lightning?p=`+ this.location +`&limit=25&client_id=EHFH6G7xUCozKVwS96iBf&client_secret=WiQfwsOzCHBqILNWfanuJDFBbOiNiuwTL22yOjiq`)
        .then(response => response.json())
        .then(data => {
					this.success = data.success
					if(this.success){
	          this.numStrikes = data.response.length
	          this.response = data.response
					} else {
						this.error = data.error.code
					}
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

#strike {
	display: flex;
	width: 80%;
	height: 5pt;
	padding: 5pt;
}

img{
	width: 15pt;
	height: 15pt;
	display: flex;
	padding: 2px;
}

table{
	border-collapse: collapse;
}

.gray-border{
	border: 1px solid gray;
	padding: 0px;
	margin: -2px;
}
</style>
