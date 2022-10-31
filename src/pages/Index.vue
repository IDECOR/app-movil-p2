<template>
  <q-page class="">
    <q-toolbar class="bg-cyan text-white">
      <q-btn flat round dense icon="pan_tool" @click="selectedTool='pan'"/>
      <q-btn flat round dense icon="info" @click="selectedTool='identify'" />
    </q-toolbar>
    <vl-map
      ref="map"
      :load-tiles-while-animating="true"
      :load-tiles-while-interacting="true"
      data-projection="EPSG:4326"
      style="height: 86vh; width: 100%;"
      @mounted="onMapMounted"
      @click="onClickHandler"
    >
      <vl-view
        :zoom.sync="zoom"
        :center.sync="center"
        :rotation.sync="rotation">
      </vl-view>

      <vl-layer-tile id="osm">
        <vl-source-osm></vl-source-osm>
      </vl-layer-tile>

      <vl-layer-tile>
        <vl-source-wms
          layer-name="Parcelas"
          :opacity="0.7"
          :crossOrigin="null"
          url="https://idecor-ws.mapascordoba.gob.ar/geoserver/idecor/parcelas/wms"
          layers="parcelas"
        />
      </vl-layer-tile>
    </vl-map>
  </q-page>
</template>

<script>
import Vue from 'vue'
import 'vuelayers/dist/vuelayers.min.css'

import { Map, OsmSource, TileLayer, TileWmsSource } from 'vuelayers'
import { ScaleLine, ZoomSlider } from 'ol/control'
import { transformExtent } from 'ol/proj'
import axios from 'axios'

Vue.use(Map)
Vue.use(TileLayer)
Vue.use(OsmSource)
Vue.use(TileWmsSource)

export default {
  name: 'PageIndex',
  data () {
    return {
      selectedTool: 'pan',
      zoom: 14,
      center: [-64.2071193, -31.4133892],
      rotation: 0
    }
  },
  methods: {
    onMapMounted () {
      this.$refs.map.$map.getControls().extend([
        new ScaleLine(),
        new ZoomSlider(),
        // new OverviewMap({
        //   collapsed: true,
        //   collapsible: true,
        //   })
      ])
    },
    onClickHandler (e) {
      if (this.selectedTool === 'identify') {
        var wmsServer = 'https://idecor-ws.mapascordoba.gob.ar/geoserver/idecor/wms'
        var bBOX = transformExtent(this.$refs.map.getView().calculateExtent(this.$refs.map.$map.getSize()),
          'EPSG:3857',
          'EPSG:4326'
        )
        var mapSize = this.$refs.map.$map.getSize()
        var x = Math.round(e.pixel[0])
        var y = Math.round(e.pixel[1])
        var paramsWMS = {
          SERVICE: 'WMS',
          VERSION: '1.1.1',
          REQUEST: 'GetFeatureInfo',
          LAYERS: 'idecor:parcelas',
          QUERY_LAYERS: 'idecor:parcelas',
          BBOX: bBOX.join(),
          FEATURE_COUNT: 1,
          WIDTH: mapSize[0],
          HEIGHT: mapSize[1],
          INFO_FORMAT: 'application/json',
          SRS: 'EPSG:4326',
          X: x,
          Y: y
        }
        axios.get(wmsServer, { params: paramsWMS })
          .then(response => {
            console.log(response.data)
            alert(JSON.stringify(response.data,null, 2))
          })
          .catch(error => {
            console.log(error)
          })
      }
    }
  }
}
</script>
