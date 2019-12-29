<template>
  <keep-alive>
  <q-list bordered class="rounded-borders">
    <q-item>
      <q-select
        v-model="city"
        use-input
        input-debounce="0"
        label="Välj område"
        :options="cities"
        @filter="filterFn"
        @input="filterCity"
        style="width: 100%"
      >
        <template v-slot:no-option>
          <q-item>
            <q-item-section class="text-grey">
              Inga resultat
            </q-item-section>
          </q-item>
        </template>
      </q-select>
        </q-item>
      <q-item clickable :to="`/${event.location.name}/${event.id}`" v-ripple v-for="event in events" :key="event.id" >
        <q-item-section avatar>
          <q-icon :color="event.color" :name="event.severity" />
        </q-item-section>

        <q-item-section>
          <q-item-label lines="1">{{event.type}}</q-item-label>
          <q-item-label caption lines="2">
            <span class="text-weight-bold">{{event.name}}</span><br>
            {{event.summary}}
          </q-item-label>
        </q-item-section>

        <q-item-section side top>
          {{event.formatted}}
        </q-item-section>
      </q-item>

      <q-separator inset="item" />
    </q-list>
  </keep-alive>
</template>

<script>
import axios from 'axios'
import TimeAgo from 'javascript-time-ago'
import sv from 'javascript-time-ago/locale/sv'

const cities = ['Blekinge län', 'Karlshamn', 'Karlskrona', 'Ronneby', 'Sölvesborg', 'Olofström', 'Munkedal', 'Kungälv ', 'Lysekil', 'Orust', 'Sotenäs', 'Stenungsund', 'Strömstad', 'Tanum', 'Uddevalla', 'Dalarnas län', 'Avesta', 'Borlänge', 'Falun', 'Gagnef', 'Hedemora', 'Leksand', 'Ludvika', 'Malung-sälen', 'Mora', 'Smedjebacken', 'Säter', 'Orsa', 'Rättvik', 'Bengtsfors', 'Dals-ed', 'Färgelanda', 'Mellerud', 'Gotland', 'Åmål', 'Gävle', 'Hofors', 'Sandviken', 'Ockelbo', 'Hallands län', 'Båstad', 'Falkenberg', 'Halmstad', 'Hylte', 'Kungsbacka', 'Laholm', 'Varberg', 'Bollnäs', 'Hudiksvall', 'Ljusdal', 'Nordanstig', 'Ovanåker', 'Söderhamn', 'Berg', 'Bräcke', 'Härjedalen', 'Krokom', 'Ragunda', 'Strömsund', 'Östersund', 'Arjeplog', 'Dorotea', 'Gällivare', 'Jokkmokk', 'Malå', 'Lycksele', 'Kiruna', 'Storuman', 'Sorsele', 'Västernorrlands län', 'Timrå', 'Norrbottens län', 'Arvidsjaur', 'Boden', 'Haparanda', 'Kalix', 'Luleå', 'Pajala', 'Piteå', 'Askersund', 'Hallsberg', 'Kumla', 'Laxå', 'Lekeberg', 'Örebro', 'Bromölla', 'Bjuv', 'Burlöv ', 'Eslöv', 'Helsingborg', 'Hässleholm', 'Höganäs', 'Hörby', 'Höör', 'Lomma', 'Lund', 'Malmö', 'Perstorp', 'Kristianstad', 'Klippan', 'Kävlinge', 'Landskrona', 'Simrimshamn', 'Sjöbo', 'Skurup', 'Staffanstorp', 'Svalöv ', 'Svedala', 'Trelleborg', 'Osby', 'Ystad', 'Ängelholm', 'Åstorp', 'Kronobergs län', 'Aneby', 'Alvesta', 'Eksjö', 'Emmaboda', 'Gnosjö', 'Hultsfred', 'Högsby', 'Lessebo', 'Ljungby', 'Markaryd', 'Mönsterås', 'Nybro', 'Nässjö', 'Jönköping', 'Kalmar', 'Sävsjö', 'Oskarshamn', 'Tranås', 'Vetlanda', 'Vimmerby', 'Värnamo', 'Växjö', 'Södermans län', 'Botkyrka', 'Eskilstuna', 'Flen', 'Haninge', 'Huddinge', 'Nacka', 'Nynäshamn', 'Nykvarn', 'Nyköping', 'Katrineholm', 'Oxelösund', 'Salem', 'Strängnäs', 'Södertälje', 'Trosa', 'Stockholms län', 'Danderyd', 'Ekerö', 'Enköping', 'Gnesta', 'Heby', 'Håbo', 'Järfälla', 'Knivsta', 'Lidingö', 'Norrtälje', 'Sigtuna', 'Solna', 'Sollentuna', 'Sundbyberg', 'Stockholm', 'Täby', 'Upplands väsby', 'Uppsala', 'Älvkarleby', 'Östhammar', 'Värmlands län', 'Arvika', 'Degerfors', 'Eda', 'Filipstad', 'Forshaga', 'Grums', 'Hammarö', 'Hagfors', 'Karlskoga', 'Karlstad', 'Kristinehamn', 'Kil', 'Munkfors', 'Storfors', 'Sunne', 'Säffle', 'Torsby', 'Västerbottens län', 'Norsjö', 'Robertsfors', 'Skellefteå', 'Umeå', 'Vännäs', 'Västra götalands län', 'Ale', 'Alingsås', 'Bollebygd', 'Borås', 'Essunga', 'Falköping', 'Grästorp', 'Gullspång', 'Göteborg', 'Götene', 'Habo', 'Herrljunga', 'Hjo', 'Härryda', 'Karlsborg', 'Lerum', 'Lidköping', 'Lilla edet', 'Mariestad', 'Mark', 'Mullsjö', 'Mölndal', 'Partille', 'Skara', 'Skövde', 'Sundsvall', 'Svenljunga', 'Tibro', 'Trollhättan', 'Ulricehamn', 'Arboga', 'Fagersta', 'Hallstahammar', 'Hällefors', 'Kungsör', 'Lindesberg', 'Ljusnarsberg', 'Nora', 'Norberg', 'Köping', 'Sala', 'Skinnskatteberg', 'Surahammar', 'Västerås', 'Bjurholm', 'Härnösand', 'Kramfors', 'Nordmaling', 'Sollefteå', 'Örnsköldsvik', 'Borgholm', 'Mörbylånga', 'Östergötlands län', 'Boxholm', 'Finspång', 'Kinda', 'Linköping', 'Mjölby', 'Motala', 'Norrköping', 'Söderköping', 'Åtvidaberg']

export default {
  data () {
    return {
      events: [],
      types: [],
      city: null,
      cities: cities
    }
  },
  created: function () {
    this.getAllEvents()
  },
  methods: {
    getAllEvents () {
      let global = this
      TimeAgo.addLocale(sv)
      const timeAgo = new TimeAgo('sv-SE')
      axios.get('https://polisen.se/api/events')
        // .then(response => (this.events = response.data))
        .then(function (response) {
          response.data.forEach(event => {
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
            global.events.push(event)
            global.types.push(event.type)
          })
          console.log(global.types)
        })
    },
    getCityEvents () {
      let global = this
      this.events = []
      this.types = []
      TimeAgo.addLocale(sv)
      const timeAgo = new TimeAgo('sv-SE')
      axios.get('https://polisen.se/api/events?locationname=' + this.city)
        .then(function (response) {
          response.data.forEach(event => {
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
            global.events.push(event)
            global.types.push(event.type)
          })
          console.log(global.types)
        })
    },
    filterCity () {
      this.getCityEvents()
    },
    filterFn (val, update) {
      if (val === '') {
        update(() => {
          this.cities = cities
        })
        return
      }

      update(() => {
        const needle = val.toLowerCase()
        this.cities = cities.filter(v => v.toLowerCase().indexOf(needle) > -1)
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
