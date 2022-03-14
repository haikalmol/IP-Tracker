<template>
  <div class="home">
    <div class="mainPreloader" v-if="mainPreloader">
      <img src="@/assets/icon-location.svg" alt="">
      <h1>ip address <span>tracker</span></h1>
    </div>
    <div class="banner" style="">
      <h2>IP Address Tracker</h2>
      <form>
        
        <input type="text" placeholder="Seach for any IP address or domain" v-model="locator" />
        <button @click="checkLocation"><img src="@/assets/icon-arrow.svg" alt="" /></button>
        <div class="preload" v-show="showPreload"></div>
      </form>
      <p class="error" v-if="error">This field is empty or wrong numbers</p>
    </div>
     <!-- <div id="mapid"></div> -->
    <div class="map">
      <l-map
      v-if="showMap"
      :zoom="zoom"
      :center="center"
      :options="mapOptions"
      style="height: 100%"
      @update:center="centerUpdate"
      @update:zoom="zoomUpdate"
    >
      <l-tile-layer
        :url="url"
        :attribution="attribution"
      />
      <l-marker :lat-lng="withPopup" >
        <l-popup>
          
        </l-popup>
        <l-icon class-name="someExtraClass"
        >
          <img src="@/assets/icon-location.svg">
        </l-icon>
      </l-marker>
    </l-map>
    </div>
    <div class="map-desc">
      <div class="map-content">
        <p>ip address</p>
        <h2>{{this.userDetails.ip}}</h2>
      </div>
      <div class="map-content">
        <p>location</p>
        <h2>{{this.userDetails.location.city}}, {{this.userDetails.location.country}} {{this.userDetails.location.geonameId}}</h2>
      </div>
      <div class="map-content">
        <p>timezone</p>
        <h2>{{this.userDetails.location.timezone}}</h2>
      </div>
      <div class="map-content">
        <p>isp</p>
        <h2>{{this.userDetails.isp}}</h2>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import { LMap, LTileLayer, LMarker, LPopup, LIcon } from "vue2-leaflet";
import { latLng  } from "leaflet";
export default {
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LPopup,
    LIcon
  },
  data() {
    return {
      locator: '',
      city: '',
      ip: '',
      userDetails: [],
      mainPreloader: false,
      showPreload: false,
      error: false,
     zoom: 15,
      center: null,
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      withPopup: null,
      withTooltip: null,
      currentZoom: 1.5,
      currentCenter: null,
      showParagraph: false,
      mapOptions: {
        zoomSnap: 1
      },
      showMap: true
    };
  },
  methods: {
    // User IP Address
    userLocation() {
      this.mainPreloader = true
      this.axios.get("https://api.ipify.org").then(response => {
        this.mainPreloader = false
        this.ip = response.data.ip
      });
    },
    // User Map
    userMap(){
      this.mainPreloader = true
      var api_key = 'at_0I0xMEJB0mGgKKIH5Uh704NeDlHyn';
      var api_url = 'https://geo.ipify.org/api/v1?';
      var url = api_url + 'apiKey=' + api_key + '&ipAddress=' + this.ip;
      this.axios.get(url).then(response => {
        this.mainPreloader = false
        this.userDetails = response.data
        this.center = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        this.withPopup = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        this.withTooltip = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        this.currentCenter = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        console.log(this.userDetails);
      });
    },
    // Map Properties
     zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    centerUpdate(center) {
      this.currentCenter = center;
    },
    // Checking for Location
    checkLocation(e){
      e.preventDefault()
      this.showPreload = true
      // RegExp for IP Address Vlidation
      var ipformat = /^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/;
      if(this.locator && this.locator.match(ipformat)){
        var api_key = 'at_0I0xMEJB0mGgKKIH5Uh704NeDlHyn';
        var api_url = 'https://geo.ipify.org/api/v1?';
        var url = api_url + 'apiKey=' + api_key + '&ipAddress=' + this.locator;
          this.axios.get(url).then(response => {
          this.showPreload = false
          this.error = false
          this.userDetails = response.data
          this.center = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
          this.withPopup = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
          this.withTooltip = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
          this.currentCenter = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
      });
      }
      else{
        this.error = true
        this.showPreload = false
      }
      
    }
  },
  created(){
    this.userLocation()
    this.userMap()  
  },
  computed:{
    // if(this.locator){

    // }
  }
};
</script>
<style>

</style>
