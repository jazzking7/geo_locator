<template>
    <div v-if="timezoneInfo">
      <p id="tz">Time Zone ID: {{ timezoneInfo.timeZoneId }} - Local Time: {{ timezoneInfo.localTime }}</p>
    </div>
</template>

<script>
export default {
  props: {newTime : Object},
  watch: {
    newTime: function () {
        this.getTimezoneAndLocalTime()
    }
  },
  data() {
    return {
      lat: null, 
      lng: null, 
      apiKey: '',  // INSERT API KEY
      timezoneInfo: null,
      error: '',
      adjOffset: 14400,
    };
  },
  methods: {
    async getTimezoneAndLocalTime() {
      this.lat = this.newTime.coords.lat()
      this.lng = this.newTime.coords.lng()
      const timestamp = Math.floor(Date.now() / 1000); 
      const apiUrl = `https://maps.googleapis.com/maps/api/timezone/json?location=${this.lat},${this.lng}&timestamp=${timestamp}&key=${this.apiKey}`;
      try {
        const response = await fetch(apiUrl);
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        const data = await response.json();
        if (data.status === 'OK') {
          const timeZoneId = data.timeZoneId;
          const rawOffset = data.rawOffset;
          const dstOffset = data.dstOffset
          // Calculate local time
          const localTime = new Date((timestamp + rawOffset + dstOffset + this.adjOffset) * 1000).toLocaleString();
          this.timezoneInfo = {
            timeZoneId,
            localTime
          };
        } else {
          throw new Error('Error fetching timezone and local time.');
        }
      } catch (error) {
        console.error('ERROR WITH TIMEZONE API:', error);
        this.timezoneInfo = null;
      }
    },
  },
};
</script>
