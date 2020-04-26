<script>
  import { onMount } from "svelte";
  import Map from "../components/map.svelte";
  import Preview from "../components/preview.svelte";
  import Details from "../components/details.svelte";
  import Chart from "../components/chart.svelte";
  import Chart2 from "../components/chart2.svelte";
  import Radar from "../components/radar.svelte";
  import News from "../components/news.svelte";
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
  .half-map {
    grid-column: span 5;
  }
  .half-info {
    grid-column: span 7;
  }
  .half-chartFull {
    grid-column: span 12;
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
  .pane-loading {
    position: fixed !important;
    width: 100vw;
    height: 100vh;
    z-index: 100;
    background-color: rgba(0, 0, 0, 0.9);
    color: white;
    text-align: center;
    padding: 20vh 0px;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
    opacity: 1;
  }
  @media (max-width: 768px) {
    .half-map {
      grid-column: span 12;
    }
    .half-info {
      grid-column: span 12;
    }
    .half-chartBig {
      grid-column: span 12;
    }
    .half-chartSmall {
      grid-column: span 12;
    }
    .mapsArea {
      height: 70vh;
    }
    .mapsArea {
      grid-template-columns: 2fr 1fr;
      grid-template-rows: 1fr 1fr;
      height: 70vh;
    }
    .mainmap {
      grid-column: 1;
      grid-row: 1 / 3;
    }
    .map2 {
      grid-column: 2;
      grid-row: 1;
    }
  }
  @media (max-width: 480px) {
    .mapsArea {
      grid-column: col / span 2;
      display: grid;
      grid-gap: 10px;
      grid-template-columns: 1fr 1fr;
      grid-template-rows: 2fr 1fr;
      height: 100vh;
    }
    .mapsArea {
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
  }
</style>

<svelte:head>
  <title>Covid 19 PT Info</title>
</svelte:head>

{#if !lastData && !timelineData && !concelhoData}
  <div class="pane-loading">
    <p>Getting Data</p>
    <div class="lds-roller">
      <div />
      <div />
      <div />
      <div />
      <div />
      <div />
      <div />
      <div />
    </div>
  </div>
{/if}
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
    <Preview {lastData} {timelineData} />

    <div class="main">
      <div class="half-chartFull">
        <Chart {timelineData} />
        <Details {timelineData} />
        <Chart2 {timelineData} />

        <Radar {lastData} />
      </div>
      <div class="half-chartBig">
        <News />

      </div>

    </div>
  </div>
</div>
