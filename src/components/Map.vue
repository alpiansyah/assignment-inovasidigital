<template>
  <div id="map-wrapper">
    <div style="height: 94vh; width: 100%; z-index: -1; ">
      <l-map
        :use-global-leaflet="false"
        ref="map"
        v-model:zoom="zoomMap"
        :center="mapCenter"
      >
        <!-- url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" -->
        <l-tile-layer
          url="http://{s}.tile.osm.org/{z}/{x}/{y}.png"
          layer-type="base"
          name="OpenStreetMap"
        ></l-tile-layer>
        <l-marker @click="mapClicked($event)" :lat-lng="[Number(item.Latitude), Number(item.Longitude)]" v-for="(item, index) in filteredMarker" :key="index">
          <l-icon
            :icon-size="dynamicSize"
            :icon-anchor="dynamicAnchor"
            icon-url="https://www.freepnglogos.com/uploads/dot-png/file-red-dot-svg-wikimedia-commons-23.png"
          />
          <l-tooltip>{{ item['Mill Name']}}</l-tooltip>
        </l-marker>

      </l-map>
      
    </div>
    <div class="modal fade" id="detailInfoModal" tabindex="-1" aria-labelledby="detailInfoModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-lg modal-dialog-centered">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="detaliInfoModalLabel">Detail</h1>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <div class="container text-left">
              <div class="row align-items-start" v-for="(item,index) in detailData" :key="index">
                <div class="col">
                  {{ item[0] }}
                </div>
                <div class="col">
                 {{ item[1] === 'None' ? '-' : item[1] }}
                </div>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";
globalThis.L = L;
import { LMap, LTileLayer, LMarker, LIcon, LTooltip } from "@vue-leaflet/vue-leaflet";
import Fuse from 'fuse.js';



export default {
  props: ['datasets', 'themecolor', 'zoom', 'mapcenter'],
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LIcon,
    LTooltip
  },
  data() {
    return {
      zoomMap: this.zoom,
      mapCenter: this.mapcenter,
      iconSize: 12,
      listLongLat: [],
      detailData: null,
    };
  },
  computed: {
    dynamicSize() {
      return [this.iconSize, this.iconSize * 1.15];
    },
    dynamicAnchor() {
      return [this.iconSize / 2, this.iconSize * 1.15];
    },
    filteredMarker(){
      return JSON.parse(JSON.stringify(this.datasets)) ;
    }
  },
  watch:{
    // zoom(newVal){
    //   // console.log(this.$refs.map)
    //   this.$refs.map.setView(this.mapcenter, newVal, {animation: true})
    // }
  },
  methods: {
    mapClicked(e) {
      const fuseConfig = {
        threshold: 0,
        keys: [
          'Longitude',
          'Latitude'
        ],
      };
      const fuse = new Fuse(JSON.parse(JSON.stringify(this.datasets)), fuseConfig);
      const searchedData = fuse.search({
        $and: [{ Longitude: String(e.latlng.lng) }, { Latitude: String(e.latlng.lat) }]}
      )
      let founded = searchedData[0].item
      let printData = Object.keys(founded).map((key) => [key, founded[key]]);
      this.detailData = printData
      const myModal = new bootstrap.Modal('#detailInfoModal', {
        keyboard: false
      })
      myModal.show()

    }
  },
  mounted(){
  },
};
</script>
<style>

.leaflet-layer,
.leaflet-control-zoom-in,
.leaflet-control-zoom-out,
.leaflet-control-attribution {
  filter: v-bind('themecolor')
}

#detailInfoModal {
  filter: v-bind('themecolor')
}
</style>
