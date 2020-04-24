<script>
  import { onMount } from "svelte";
  import Map from "../components/map.svelte";
  import Preview from "../components/preview.svelte";
  import Details from "../components/details.svelte";
  import Chart from "../components/chart.svelte";
  import Radar from "../components/radar.svelte";
  import Pie from "../components/pie.svelte";
  import { CovidPT } from "covid19-api-pt";

  var lastData, timelineData, concelhoData;

  getData();

  async function getData() {
    lastData = await new CovidPT().last();
    timelineData = await new CovidPT().all();

    fetch(
      "https://services.arcgis.com/CCZiGSEQbAxxFVh3/arcgis/rest/services/COVID19_Concelhos_V/FeatureServer/0/query?f=json&where=ConfirmadosAcumulado_Conc%3E0%20AND%20Data_Conc%3C%3Dtimestamp%20%272021-04-22%2022%3A59%3A59%27&returnGeometry=false&spatialRel=esriSpatialRelIntersects&outFields=*&orderByFields=ConfirmadosAcumulado_Conc%20desc&resultType=standard&cacheHint=true",
      {
        referrer: "https://www.arcgis.com/apps/opsdashboard/index.html",
        referrerPolicy: "no-referrer-when-downgrade",
        body: null,
        method: "GET",
        mode: "cors",
        credentials: "omit"
      }
    )
      .then(response => response.json())
      .then(result => {
        concelhoData = result.features;
      })
      .catch(error => {
        console.error("Error:", error);
      });
  }

  onMount(() => {
    mapboxgl.setRTLTextPlugin(
      "https://cdn.maptiler.com/mapbox-gl-js/plugins/mapbox-gl-rtl-text/v0.1.2/mapbox-gl-rtl-text.js"
    );
    mapboxgl.accessToken =
      "pk.eyJ1IjoiYmpkaW9nbyIsImEiOiJjazg3bW40dnkwbjYwM2htbWc1NnBidzQ2In0.lh4trQ8-6vDRegpJWs6mBw";
  });
</script>

<style>
  .main {
    display: grid;
    grid-template-columns: repeat(12, minmax(0, 1fr));
    grid-gap: 16px;
  }
  .half {
    grid-column: span 6;
  }
  .half-map {
    grid-column: span 5;
  }
  .half-info {
    grid-column: span 7;
  }

  .half-chartBig {
    grid-column: span 8;
  }
  .half-chartSmall {
    grid-column: span 4;
  }
  .mapsArea {
    grid-column: col / span 2;
    display: grid;
    grid-gap: 10px;
    grid-template-columns: 1fr 1fr;
    grid-template-rows: 2fr 1fr;
    height: 100vh;
  }

  .mainmap {
    grid-column: 1 / 3;
    grid-row: 1;
  }

  .map2 {
    grid-column: 1;
    grid-row: 2;
  }

  .map3 {
    grid-column: 2;
    grid-row: 2;
  }
</style>

<svelte:head>
  <title>Covid 19 PT Info</title>
</svelte:head>

<div class="main">
  <div class="half-map">
    <div class="mapsArea">
      <div class="mainmap">
        <Map mapa="main" {timelineData} {concelhoData} {mapboxgl} />
      </div>
      <div class="map2">
        <Map mapa="acores" {timelineData} {concelhoData} {mapboxgl} />
      </div>
      <div class="map3">
        <Map mapa="madeira" {timelineData} {concelhoData} {mapboxgl} />
      </div>
    </div>
  </div>
  <div class="half-info">
    <Preview {lastData} />

    <div class="main">
      <div class="half-chartBig">
        <Chart {timelineData} />
        <Details {timelineData} />
        <Radar {lastData} />

      </div>
      <div class="half-chartSmall">
        <Pie {lastData} />

      </div>

    </div>
  </div>
</div>
