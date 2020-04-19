<script>
  import Map from "../components/map.svelte";
  import Preview from "../components/preview.svelte";

  var lastDdata, timelineData, concelhoData, distritosData;

  getData();

  async function getData() {
    const r1 = await fetch(
      "https://services.arcgis.com/CCZiGSEQbAxxFVh3/arcgis/rest/services/COVID19Portugal_UltimoRel/FeatureServer/0/query?f=json&where=1%3D1&returnGeometry=false&spatialRel=esriSpatialRelIntersects&outFields=*&resultOffset=0&resultRecordCount=32000&resultType=standard&cacheHint=true",
      {
        referrer: "https://www.arcgis.com/apps/opsdashboard/index.html",
        referrerPolicy: "no-referrer-when-downgrade",
        body: null,
        method: "GET",
        mode: "cors",
        credentials: "omit"
      }
    );
    lastDdata = (await r1.json()).features;
    const r2 = await fetch(
      "https://services.arcgis.com/CCZiGSEQbAxxFVh3/arcgis/rest/services/COVID19Portugal_view/FeatureServer/0/query?f=json&where=1%3D1&returnGeometry=false&spatialRel=esriSpatialRelIntersects&outFields=*&orderByFields=datarelatorio%20asc&resultOffset=0&resultRecordCount=32000&resultType=standard&cacheHint=true",
      {
        referrer: "https://www.arcgis.com/apps/opsdashboard/index.html",
        referrerPolicy: "no-referrer-when-downgrade",
        body: null,
        method: "GET",
        mode: "cors",
        credentials: "omit"
      }
    );
    timelineData = (await r2.json()).features;

    const r3 = await fetch(
      "https://services.arcgis.com/CCZiGSEQbAxxFVh3/arcgis/rest/services/COVID19_Concelhos_V/FeatureServer/0/query?f=json&where=ConfirmadosAcumulado_Conc%3E0%20AND%20Data_Conc%3E%3Dtimestamp%20%272020-04-17%2023%3A00%3A00%27%20AND%20Data_Conc%3C%3Dtimestamp%20%272020-04-18%2022%3A59%3A59%27&returnGeometry=false&spatialRel=esriSpatialRelIntersects&outFields=*&orderByFields=ConfirmadosAcumulado_Conc%20desc&resultOffset=0&resultRecordCount=318&resultType=standard&cacheHint=true",
      {
        referrer: "https://www.arcgis.com/apps/opsdashboard/index.html",
        referrerPolicy: "no-referrer-when-downgrade",
        body: null,
        method: "GET",
        mode: "cors",
        credentials: "omit"
      }
    );
    concelhoData = (await r3.json()).features;

    const r4 = await fetch(
      "https://services.arcgis.com/CCZiGSEQbAxxFVh3/arcgis/rest/services/COVID19Portugal_UltimoRel/FeatureServer/1/query?f=json&where=1%3D1&returnGeometry=false&spatialRel=esriSpatialRelIntersects&outFields=*&orderByFields=dist_casosconf%20desc&outSR=102100&resultOffset=0&resultRecordCount=30&resultType=standard&cacheHint=true",
      {
        referrer: "https://www.arcgis.com/apps/opsdashboard/index.html",
        referrerPolicy: "no-referrer-when-downgrade",
        body: null,
        method: "GET",
        mode: "cors",
        credentials: "omit"
      }
    );
    distritosData = (await r4.json()).features;
  }
</script>

<style>

</style>

<svelte:head>
  <title>Covid 19 PT Info</title>
</svelte:head>

<Map {concelhoData} {distritosData} />
<Preview {lastDdata} />
<h1>Great success!</h1>
