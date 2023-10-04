<template>
  <div class="app_title"><h1>Geo-Locator</h1></div>
    <div class="LR">
  <p v-if="Latest_Search !== null">Searched: {{ Latest_Search.name }} - Coords: {{ Latest_Search.coords }} </p>
  <TimeZone :newTime="Latest_Search"/>
  </div>
  <LocationSearch @LatestSearch="updateLatest" />
</template>

<script>
import LocationSearch from './components/LocationSearch.vue'
import TimeZone from './components/TimeZone.vue'

export default {
  name: 'App',
  components: {
    LocationSearch,
    TimeZone
  },
  data(){
    return {
        Latest_Search: null,
    }
  },
  methods: {
    updateLatest(data) {
      this.Latest_Search = data
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.app_title h1 {
  background: #0faf87;
  color: white; font-family: 'Helvetica Neue', sans-serif; 
  font-size: 25px; font-weight: bold; padding-top: 5px; padding-bottom: 5px;
   padding-left: 1px; padding-right: 1px;
  letter-spacing: -1px; line-height: 1; text-align: center;
  width: 80%; margin: 0 auto; margin-bottom: 10px;
}
.LR p {
  background: #089999;
  width: 80%;
  margin: 0 auto;
  color: white;
  font-weight: 300;
}
.LR div{
  margin-bottom: 10px
}
</style>
