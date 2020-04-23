<script>
  import Map from "../components/map.svelte";
  import Preview from "../components/preview.svelte";
  import { CovidPT } from "covid19-api-pt";

  var lastData, timelineData, concelhoData, distritosData;

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
</script>

<style>

</style>

<svelte:head>
  <title>Covid 19 PT Info</title>
</svelte:head>

<Map {timelineData} {concelhoData} />
<Preview {lastData} />
<h1>Great success!</h1>
