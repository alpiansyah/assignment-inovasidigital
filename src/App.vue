<script>

import Map from './components/Map.vue'
import HeaderNav from './components/Header.vue'
import FilterModal from './components/Modal/Filter.vue'
import SearchModal from './components/Modal/Search.vue'
import StatisticModal from './components/Modal/Statistic.vue'
import mainData from './dataset/inodigi.json'
import Fuse from 'fuse.js';


export default {
  components: {Map, HeaderNav, FilterModal, StatisticModal, SearchModal},
  name: 'app',
  data() {
    return {
      mainData: mainData,
      mapData: null,
      splash: true,
      zoom: 2,
      mapCenter: [0,0]
    }
  },
  computed:{
    themeColor: {
      get: function () {
        return 'invert(100%) hue-rotate(180deg) brightness(95%) contrast(90%)'
      },
      set: function (newValue) {
        return newValue
      }
    },
  },
  methods: {
    filterDatasets(value){
      this.mapData = []
      if(value[1]==="") {
        this.mapData = this.mainData
        this.zoom = 2
        this.mapCenter = [0,0]
      } else {
        const fuseConfig = {
          threshold: 0,
          keys: [
            value[0],
          ],
        };
        const fuse = new Fuse(JSON.parse(JSON.stringify(this.mainData)), fuseConfig);
        const filtered = fuse.search(value[1])
        filtered.forEach(element => {
          this.mapData.push(element.item)
        });
        this.zoom = 4
        this.mapcenter = [Number(this.mapData[0]['Latitude']), Number(this.mapData[0]['Longitude'])] 
      }
      
      this.$forceUpdate() 
    }
  },
  mounted(){
    console.log('asdf')
    this.mapData = this.mainData
    setTimeout(() => {
      this.splash = false
    }, 1500);
  }
}
</script>

<template>
  <div>
    
    <transition>
      <div id="splashScreen" v-if="splash">
        <h1>Inovasi Digital Assignment</h1>
      </div>
    </transition>
    
      <!-- HEADER COMPONENT-->
      <HeaderNav :themecolor="themeColor"/>
  
    <!-- MAP COMPONENT -->
      <Map :datasets="mapData" :themecolor="themeColor" :zoom="zoom" :mapcenter="mapCenter" />


      <!-- MODALS COMPONENT -->
      <StatisticModal :datasets="mainData" :themecolor="themeColor"/>
      <SearchModal :datasets="mainData" :themecolor="themeColor"/>
      <FilterModal @submitted="filterDatasets($event)" :datasets="mainData" :themecolor="themeColor"/>
   
  </div>
  
</template>

<style scoped>
#splashScreen {
  width: 100%;
  height: 100vh;
  position: absolute;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 100000;
  color:white;
}
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
