<template>
  <keep-alive>
  <q-list bordered class="rounded-borders">
  <q-item>
<iframe class="mapview" :src="event.mapurl"></iframe>
  </q-item>
      <q-item>
        <q-item-section>
          <q-item-label>Händelsetidpunkt</q-item-label>
          <q-item-label caption lines="2">{{event.datetime}}</q-item-label>
        </q-item-section>
        <q-item-section side top>
          <q-item-label caption>{{event.formatted}}</q-item-label>
        </q-item-section>
      </q-item>
      <q-item>
        <q-item-section>
          <q-item-label>Händelseplats</q-item-label>
          <q-item-label caption>{{event.location.name}}</q-item-label>
        </q-item-section>
      </q-item>
      <q-item>
        <q-item-section>
          <q-item-label>Titel</q-item-label>
          <q-item-label caption>{{event.name}}</q-item-label>
        </q-item-section>
      </q-item>
      <q-item>
        <q-item-section>
          <q-item-label>Sammanfattning</q-item-label>
          <q-item-label caption>{{event.summary}}</q-item-label>
        </q-item-section>
      </q-item>
      <q-item clickable ripple class="link" @click.native="onClick(event.url)">
        <q-item-section>
          <q-item-label>Länk</q-item-label>
          <q-item-label caption>Gå till polisen.se</q-item-label>
        </q-item-section>
        <q-item-section side>
          <q-icon name="chevron_right" color="gray" />
        </q-item-section>
      </q-item>
    </q-list>
  </keep-alive>
</template>
<script>
import axios from 'axios'
import TimeAgo from 'javascript-time-ago'
import sv from 'javascript-time-ago/locale/sv'
import { openURL } from 'quasar'

export default {
  data () {
    return {
      event: {},
      types: []
    }
  },
  mounted: function () {
    this.getAllEvents()
  },
  methods: {
    onClick: function (link) {
      openURL(link)
    },
    getAllEvents () {
      let global = this
      TimeAgo.addLocale(sv)
      const timeAgo = new TimeAgo('sv-SE')
      axios.get('https://polisen.se/api/events?locationname=' + global.$route.params.city)
        // .then(response => (this.events = response.data))
        .then(function (response) {
          response.data.forEach(event => {
            if (parseInt(event.id) === parseInt(global.$route.params.id)) {
              let geolocation = event.location.gps.split(',')
              let long = parseFloat(geolocation[0])
              let lat = parseFloat(geolocation[1])
              event.long = long
              event.lat = lat
              event.bbox = {
                minlong: (long - 0.05),
                minlat: (lat - 0.05),
                maxlong: (long + 0.05),
                maxlat: (lat + 0.05)
              }
              event.mapurl = 'https://www.openstreetmap.org/export/embed.html?bbox=' + event.bbox.minlat + ',' + event.bbox.minlong + ',' + event.bbox.maxlat + ',' + event.bbox.maxlong + '&layer=mapnik&marker=' + event.long + ',' + event.lat
              let datetime = event.datetime.replace(' +01:00', '')
              datetime = datetime.replace(' +02:00', '')
              let date = new Date(datetime)
              event.formatted = timeAgo.format(date)
              let type = 'fas fa-question'
              let color = 'primary'
              if (
                event.type.toLowerCase().includes('rån') ||
                event.type.toLowerCase().includes('misshandel') ||
                event.type.toLowerCase().includes('detonation') ||
                event.type.toLowerCase().includes('vapen') ||
                event.type.toLowerCase().includes('mord') ||
                event.type.toLowerCase().includes('dråp') ||
                event.type.toLowerCase().includes('försvunnen') ||
                event.type.toLowerCase().includes('överfall') ||
                event.type.toLowerCase().includes('våldtäkt') ||
                event.type.toLowerCase().includes('inbrott')) {
                type = 'warning'
                color = 'red'
              }
              if (
                event.type.toLowerCase().includes('skadegörelse') ||
                event.type.toLowerCase().includes('bråk') ||
                event.type.toLowerCase().includes('stöld') ||
                event.type.toLowerCase().includes('narkotika') ||
                event.type.toLowerCase().includes('fyll') ||
                event.type.toLowerCase().includes('trafikbr')) {
                type = 'warning'
                color = 'orange'
              }
              if (event.type.toLowerCase().includes('olycka')) {
                type = 'fas fa-ambulance'
                color = 'secondary'
              }
              if (event.type.toLowerCase().includes('sammanfatt')) {
                type = 'info'
              }
              if (event.type.toLowerCase().includes('brand')) {
                type = 'fas fa-fire'
                color = 'orange'
              }
              event.color = color
              event.severity = type
              global.event = event
            }
          })
          console.log(global.event)
        })
    }
  }
}
</script>

<style>
.mapview {
  border: 0px;
  height: 250px;
  width: 100%;
}
</style>
