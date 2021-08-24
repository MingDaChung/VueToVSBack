<template>
  <div class="hello">
    <div class="search_box">
      <span>Please Input Address：</span>
      <input
        type="text"
        v-model.lazy.trim="keyword"
        class="input"
        @change="callApi"
      />
    </div>
    <table class="table table_border" id="idData" v-if="datas.length > 0">
      <thead>
        <tr>
          <td>Map</td>
          <td colspan="2">sno</td>
          <td colspan="4">sna</td>
          <td>tot</td>
          <td>sarea</td>
          <td colspan="3">mday</td>
          <td colspan="2">lat</td>
          <td colspan="2">lng</td>
          <td colspan="4">ar</td>
        </tr>
      </thead>
      <tr v-for="data in datas" :key="data.sno">
        <td>
          <button class="map_icon" @click="ShowTheMap(data)">
            <i class="bx bx-map"></i>
          </button>
        </td>
        <td colspan="2">{{ data.sno }}</td>
        <td colspan="4">{{ data.sna }}</td>
        <td>{{ data.tot }}</td>
        <td>{{ data.sarea }}</td>
        <td colspan="3">{{ data.mday }}</td>
        <td colspan="2">{{ data.lat }}</td>
        <td colspan="2">{{ data.lng }}</td>
        <td colspan="4">{{ data.ar }}</td>
      </tr>
    </table>

    <l-map v-if="showMap" :zoom="zoom" :center="center" class="Map">
      <l-tile-layer :url="url" :attribution="attribution" />
      <l-marker :lat-lng="marker" />
      <l-icon-default :image-path="path" />
    </l-map>

    <nav aria-label="Page navigation example">
      <ul class="pagination">
        <li class="page-item">
          <button type="button" class="page-link" @click="page--">
            <i class="bx bxs-chevron-left"></i>
          </button>
        </li>
        <li class="page-item">
          {{ page }}
        </li>
        <li class="page-item">
          <button type="button" @click="page++" class="page-link">
            <i class="bx bxs-chevron-right"></i>
          </button>
        </li>
      </ul>
    </nav>
  </div>
</template>

<script>
import axios from "axios";
import { latLng } from "leaflet";
import { LMap, LTileLayer, LMarker } from "vue2-leaflet";
export default {
  name: "HelloWorld",
  components: {
    LMap,
    LTileLayer,
    LMarker,
  },
  data() {
    return {
      keyword: "",
      datas: [],
      pageconut: 0,

      zoom: 16,
      path: "/images/",
      center: [],
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      marker: latLng(),
      showMap: false,
      page: 1,
      logic: "",
    };
  },
  mounted() {
    this.callApi();
    // console.log(this.data);
  },
  watch: {
    page: function() {
      if (this.page >= this.pageconut) {
        this.page = this.pageconut;
      } else if (this.page <= 1) {
        this.page = 1;
      }
      this.callApi();
    },
  },
  methods: {
    callApi() {
      axios
        .get("https://localhost:44306/api/youbike", {
          params: { keyword: this.keyword, page: this.page },
        })
        .then((response) => {
          this.datas = response.data.result;
          this.pageconut = response.data.totalPages;
        });
    },
    ShowTheMap(data) {
      if (!this.showMap) {
        this.showMap = true;
      } else {
        if (data.sno === this.logic) this.showMap = false;
      }
      this.logic = data.sno;

      this.center = [data.lat, data.lng];
      this.marker = latLng(data.lat, data.lng);
    },
  },
};
</script>

<style scoped>
.search_box {
  font-size: 1.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 7vh;
}

.bx-x {
  font-size: 1.7rem;
}
table tr td {
  border: 1px solid black;
}
.table {
  width: 100%;
  height: 70vh;
  border-collapse: collapse;
  text-align: center;
  table-layout: fixed;
}
td:not(:hover) {
  vertical-align: middle;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  /* transition: 3s; */
}
td {
  font-size: 15px;
  word-break: break-word;
}
.Map {
  width: 50%;
  height: 500px;
  position: fixed;
  top: 10vh;
  left: 25%;
}
</style>
