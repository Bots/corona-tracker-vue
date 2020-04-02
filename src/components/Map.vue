<template>
  <MglMap
    :accessToken="accessToken"
    :mapStyle.sync="mapStyle"
  >
    <MglMarker v-for="(l, key) in locations" :key="key" :coordinates="[l.coordinates.longitude, l.coordinates.latitude]" @click="activateMarker(l)">
      <MglPopup>
        <VCard>
          <div>Country: {{ l.country }}</div>
          <div v-if="l.province">Province: {{ l.province }}</div>
          <div>Number of confirmed cases: {{ l.latest.confirmed }}</div>
        </VCard>
      </MglPopup>
    </MglMarker>
  </MglMap>
</template>

<script>
  import Mapbox from "mapbox-gl";
  import { MglMap, MglPopup, MglMarker } from "vue-mapbox"

  export default {
    name: 'Map',

    components: {
      MglMap,
      MglPopup,
      MglMarker,
    },

    data() {
      return {
        accessToken: process.env.VUE_APP_MAPBOX_TOKEN,
        mapStyle: 'mapbox://styles/mapbox/dark-v10',
        locations: null,
        activeMarker: null,
        cases: null,
        province: null,
        country: null
      }
    },

    created() {
      this.mapbox = Mapbox;
      this.getData();
    },

    methods: {
      getData: function () {
        fetch('https://coronavirus-tracker-api.herokuapp.com/v2/locations')
          .then(response => response.json())
          .then(data => {
            const locations = data.locations;
            this.locations = locations;
          })
      },

      activateMarker: function (l) {
        if(l.country && !l.province) {
          const country = l.country;
          this.country = country;
          const cases = l.latest.confirmed;
          this.cases = cases;
        } else {
          const province = l.province;
          this.province = province;
          const cases = l.latest.confirmed;
          this.cases = cases;
        }
      }
    }
  }
</script>
