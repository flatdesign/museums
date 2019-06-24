<template>
  <div class="map-wrapper">
    <h2>Музеи Москвы</h2>
    <ul class="tabs">
      <li><a href="#" @click.prevent="showMap = false">Показать список</a></li>
      <li><a href="#" @click.prevent="showMap = true">Показать на карте</a></li>
    </ul>

    <ul class="museum-list" v-if="!showMap">
      <li 
        v-for="(museum, index) in museums"
        :key="index"
      >
        <span>{{museum.properties.name}}</span> - {{museum.properties.description}}
      </li>
    </ul>
    <yandex-map v-else
      :settings="settings"
      :coords="[55.756802, 37.622720]"
      zoom="10"
      style="width: 100%; height: 600px;"
      :controls="['trafficControl', 'fullscreenControl', 'typeSelector', 'zoomControl']"
      @map-was-initialized="initHandler">

        <ymap-marker
          v-for="(museum, index) in museums"
          :key="index"
          :coords="museum.geometries[0].coordinates.reverse()"


          marker-id="1"
          marker-type="placemark"
          
          hint-content="Hint content 1"
          :balloon="{header: museum.properties.name, body: museum.properties.description}"
          cluster-name="1"
        ></ymap-marker>

    </yandex-map>
  </div>
</template>

<script>
  import axios from 'axios';
  import { yandexMap, ymapMarker } from 'vue-yandex-maps'

  export default {
    components: {
      yandexMap,
      ymapMarker
    },
    data() {
      return {
        showMap: false,
        settings: {
          apiKey: '0184cc26-ee3a-4ecf-b275-38a508670105',
          lang: 'ru_RU',
          version: '2.1'
        },
        museums: []
      }
    },

    methods: {
      async loadMuseums() {
        try {
          let responce = await axios.get("https://search-maps.yandex.ru/v1/", {
            params: {
              "apikey": "2a8c3c2a-da28-4fc0-806d-0e91b034b8d6",
              "text": "музеи",
              "type": "biz",
              "lang": "ru_RU",
              "ll": "37.62335600000006,55.75645",   // 55.756802, 37.622720
              "spn": "0.03,0.03",
              "results": "30",
              "rspn": "true"
            }
          });

          this.museums = responce.data.features;
        } catch(e) {
          console.log(e);
          alert("Не удалось ")
        }
      },
      initHandler() {

      }
      
    },
    created() {
      this.loadMuseums();
    }
  }
</script>

<style lang="scss" scoped>
  h2 {
    margin-top: 0;
    font-weight: 700;
  }
  .tabs {
    display: flex;
    padding-left: 0;
    list-style: none;
    li {
      margin-right: 10px;
      a {
        &:hover, &:focus {
          font-weight: 700;
        }
      }
    }  
  }
  .museum-list {
    padding-left: 0;
    list-style: none;
    li {
      border: 1px solid lightgrey;
      padding: 10px 5px;
      span {
        font-weight: 700;
        
      }
    }
  }
</style>

