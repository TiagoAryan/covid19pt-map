<script>
  import { onMount } from "svelte";

  export let timelineData;
  export let concelhoData;
  export let type = true;

  $: concelhoData, placeAllCircles();

  onMount(() => {
    var bounds = [[36, -6], [42, -9]];

    let map = L.map("map", {
      minZoom: 7,
      maxZoom: 10,
      maxBounds: bounds
    }).setView([39, -8], 7);

    let gl = L.mapboxGL({
      attribution:
        '<a href="https://www.maptiler.com/copyright/" target="_blank">© MapTiler</a> <a href="https://www.openstreetmap.org/copyright" target="_blank">© OpenStreetMap contributors</a>',
      accessToken:
        "pk.eyJ1IjoiYmpkaW9nbyIsImEiOiJjazg3bW40dnkwbjYwM2htbWc1NnBidzQ2In0.lh4trQ8-6vDRegpJWs6mBw",
      maxZoom: 8,
      style:
        "https://api.maptiler.com/maps/5ce0b2a2-d5dc-44ae-84f3-7211439b9474/style.json?key=TLbKST4hnYUY3nc3yvDh"
    }).addTo(map);

    map.on("click", onMapClick);

    //Listener function taking an event object
    function onMapClick(e) {
      console.info("onMapClick");
    }
  });

  function placeAllCircles() {
    if (!timelineData && !concelhoData) return;
    if (type) {
      let data = concelhoData.filter(e => "COIMBRA" == e.attributes.Concelho);
      console.info(data);
    } else {
      console.info(timelineData);
    }
  }

  function changeType() {
    type = !type;
  }
</script>

<style>
  #map {
    position: fixed !important;
    width: 100vw;
    height: 100vh;
  }
</style>

<div id="map" />
