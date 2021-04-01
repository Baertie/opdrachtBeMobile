<template>
  <div id="map" class="basemap"></div>
</template>

<script>
import mapboxgl from 'mapbox-gl'
import 'mapbox-gl/dist/mapbox-gl.css'
import axios from 'axios'

export default {
  name: 'Main',
  data () {
    return {
      info: null,
      errored: false,
      interval: null
    }
  },
  methods: {
    getData: () => {
      axios
        .get('https://data.stad.gent/api/records/1.0/search/?dataset=real-time-bezettingen-fietsenstallingen-gent&q=&facet=facilityname')
        .then(response => {
          this.info = response.data
        })
        .catch(error => {
          console.log(error)
          this.errored = true
        })
      mapboxgl.accessToken = 'pk.eyJ1IjoiYmFlcnRpZSIsImEiOiJja211b2YzenUweDRvMnFtd3VkNm9ya2tjIn0.Id8ek32Tab0LJvRYIemcsw'

      // eslint-disable-next-line no-new
      var map = new mapboxgl.Map({
        container: 'map', // container id
        style: 'mapbox://styles/mapbox/streets-v11', // style URL
        center: [3.717424, 51.054340], // starting position [lng, lat]
        zoom: 12, // starting zoom
        trackResize: true
      })
      map.addControl(new mapboxgl.NavigationControl())

      map.on('load', () => {
        this.info.records.forEach(record =>
          new mapboxgl.Marker().setLngLat(record.geometry.coordinates).setPopup(new mapboxgl.Popup().setText(`Plaats: ${record.fields.facilityname} Totaal aantal plaatsen: ${record.fields.totalplaces} Aantal vrije plaatsen: ${record.fields.freeplaces}`)).addTo(map)
        )
      })
    },
    makeMap: () => {
    }
  },
  mounted () {
    this.interval = setInterval(this.getData, 60000)
    this.getData()
    // this.makeMap()
  },
  beforeUnmount () {
    clearInterval(this.interval)
  }
}
</script>

<style scoped>
.basemap {
  width: 100vw;
  height: 100vh;
}
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
