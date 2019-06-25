<template>
  <div class="map-wrapper">
    <div class="controls">
      <h2 class="title">Музеи Москвы</h2>
      <ul class="tabs">
        <li><a href="#" @click.prevent="showMap = false" :class="!showMap ? 'active': ''">Показать список</a></li>
        <li><a href="#" @click.prevent="showMap = true" :class="showMap ? 'active': ''">Показать на карте</a></li>
      </ul>
    </div>
    
    <div class="md-layout map-table" v-if="!showMap && museums.length">
      <div class="md-layout-item md-size-100">
        <md-card>
          <md-card-header class="md-card-header-icon md-card-header-green">
            <div class="card-icon">
              <md-icon>assignment</md-icon>
            </div>
            <h4 class="title">Музеи москвы</h4>
          </md-card-header>
          <md-card-content>
            <md-table v-model="museums" table-header-color="green">
              <md-table-row slot="md-table-row" slot-scope="{ item }">
                <md-table-cell md-label="id">{{ item.properties.id }}</md-table-cell>
                <md-table-cell md-label="Название">{{ item.properties.name }}</md-table-cell>
                <md-table-cell md-label="Адрес">{{item.properties.description}}</md-table-cell>
              </md-table-row>
            </md-table>
          </md-card-content>
        </md-card>
      </div>
    </div>

    <div class="map" v-show="showMap">
      <yandex-map 
        :settings="settings"
        :coords="[55.756802, 37.622720]"
        :controls="['trafficControl', 'fullscreenControl', 'typeSelector', 'zoomControl']"
        zoom="10"
        style="width: 100%; height: 600px;"
      >
        <ymap-marker
          v-for="museum in museums"
          :key="museum.properties.id"
          marker-type="placemark"
          :coords="museum.geometry.coordinates.slice().reverse()"
          :marker-id="museum.properties.id"
          :balloon="{header: museum.properties.name, body: museum.properties.description}"
          :init-without-markers="false"
        ></ymap-marker>
      </yandex-map>
    </div>
    
  </div>
</template>

<script>
  import axios from 'axios';
  import { yandexMap, ymapMarker } from 'vue-yandex-maps';

  export default {
    name: "MoscowMap",
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
        museums: [],
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
              "ll": "37.62335600000006,55.75645",
              "spn": "0.03,0.03",
              "results": "30",
              "rspn": "true"
            }
          });

          this.museums = responce.data.features;
        } catch(e) {
          console.log(e);
          alert("Не удалось загрузить музеи")
        }
      }
    },
    created() {
      this.loadMuseums();
    }
  }
</script>

<style lang="scss" scoped>
  .controls {
    padding-left: 15px;
    padding-right: 15px;
  }
  .title {
    margin-top: 0;
    margin-bottom: 10px;
    font-weight: 700;
  }
  .map {
    padding: 0 15px;
    margin-bottom: 20px;
  }
  .tabs {
    display: flex;
    padding-left: 0;
    list-style: none;
    li {
      margin-right: 10px;
      a {
        color: #000;
        font-weight: 700;
        &.active {
          color: #89229b;
        }
      }
    }
  }
</style>

