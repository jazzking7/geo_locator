<template>
    <div id="mbox">
    <div id="search"> <button @click="getCurrentLocation">Get Current Location</button> </div>
    <div id="search"> 
        <div id="prompt">
        <label>Enter location: </label>
        <input type="text" v-model="tempLocation" @keyup.enter="searchLocation">
        </div>
        <button @click="searchLocation">Search Location</button>
    </div>
    </div>
    <p id="error" v-if="error_msg">{{ error_msg }}</p>
    <div ref="sMap" class="google-map"></div>
    <ShowResults :allResults="{len: num_markers, results: search_results}" @LocationDeleted="UpdateView" />
</template>

<script>
import ShowResults from './ShowResults.vue'
export default {
    components: {
        ShowResults
    },
    watch: {
        num_markers: function () {
        this.displayMarkers()
        }
    },
    emits: ['LatestSearch'],
    data(){
        return {
            apiKey : "", // INSERT API KEY
            tempLocation: '',
            location_name: '',
            latest_search: null,
            search_results: [],
            num_markers: 0,
            error_msg: '',
            dmap: null
        }
    },
    mounted() {
        if (window.google) {
          this.initializeMap()
        }
        const script = document.createElement("script");
        script.src = `https://maps.googleapis.com/maps/api/js?key=${this.apiKey}&libraries=places`;
        script.defer = true;
        script.async = true;
        script.onload = () => {this.initializeMap()};
        document.head.appendChild(script);
    },
    methods: {
        initializeMap(){
            this.dmap = new google.maps.Map(this.$refs.sMap)
        },
        searchLocation()
        {
        this.error_msg = ""
        const service = new google.maps.places.PlacesService(this.dmap)
        const request = { query: this.tempLocation, fields: ["geometry"],}
        this.location_name = this.tempLocation
        service.findPlaceFromQuery(request, (results, status) => {
            if (status === "OK" && results[0]) 
            {
                this.latest_search =  { name: this.location_name, coords : results[0].geometry.location }
                console.log(results[0].geometry.location)
                this.$emit('LatestSearch', this.latest_search);
                this.addUniqueLocation(this.latest_search)
            }
            else {
                this.latest_search = null 
                this.error_msg = "Location Not Found!" }
      })
      this.tempLocation = ''
     },

     addUniqueLocation( newr) {
        const newLat = newr.coords.lat();
        const newLng = newr.coords.lng();
        if (!this.search_results.some((r) => r.coords.lat() === newLat && r.coords.lng() === newLng)) {
            this.search_results.push(newr)
            this.num_markers = this.search_results.length
            }
        },

     displayMarkers(){
        if (this.search_results.length){
            const latest = this.search_results.length-1
            const options = {
                center: { lat: this.search_results[latest].coords.lat(), lng: this.search_results[latest].coords.lng() }, // Default center
                zoom: 12, 
            };
            this.dmap = new google.maps.Map(this.$refs.sMap, options);
                this.search_results.forEach(
                    (result) => {
                        new google.maps.Marker({
                        map: this.dmap,
                        position: result.coords,
                    }
                    );
                }
            )
        } else {
            this.dmap = new google.maps.Map(this.$refs.sMap);
        }
     },
     UpdateView(data){
        this.search_results = data
        this.num_markers = this.search_results.length
     },
     getCurrentLocation(){
      // Get the user's current position
      navigator.geolocation.getCurrentPosition(
        (position) => {
          const { latitude, longitude } = position.coords;
          const userLocation = new window.google.maps.LatLng(latitude, longitude)
          this.dmap.setCenter(userLocation)
          this.latest_search =  { name: "Current Location", coords : userLocation }
          this.addUniqueLocation(this.latest_search)
        },
        (error) => {
          console.error('Error getting current location: ', error);
        }
      );
    },

    },
}
</script>

<style>
    label{
        color: #aaa;
        font-size: 15px;
        text-transform: uppercase;
        font-weight: bold;
    }
    input{
        padding: 5px 5px;
        width: 60%;
        box-sizing: border-box;
        border: 2px solid #aaa;
        border-bottom: 2px solid #aaa;
        color: #555;
    }
    button {
        background: #0faf87;
        border: 0;
        padding: 10px 20px;
        margin-top: 10px;
        color: white;
        border-radius: 20px;
        margin-bottom: 10px;
        
    }
    .google-map{
        width: 80%;
        height: 400px;
        margin: 0 auto;
    }
    #error{
        color: #ff0062;
        font-size: 0.8em;
        font-weight: bold;
    }
    #search {
         box-shadow: 0 0 5px 0 rgba(0,0,0,0.2);
         width: 80%;
         margin: 0 auto;
    }
    #mbox{
        margin-bottom: 10px;
    }
    #prompt{
        padding-top: 10px;
    }
</style>