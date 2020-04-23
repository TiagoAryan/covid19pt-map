<script>
  import { onMount } from "svelte";

  export let concelhoData;
  var map;

  let Municip;

  $: map && concelhoData, placeAllCircles();
  $: Municip && getMunicip();

  onMount(() => {
    let bounds = [[30, -6], [43, -31]];
    let selectedConselho;
    let hoveresConselho;

    mapboxgl.setRTLTextPlugin(
      "https://cdn.maptiler.com/mapbox-gl-js/plugins/mapbox-gl-rtl-text/v0.1.2/mapbox-gl-rtl-text.js"
    );
    mapboxgl.accessToken =
      "pk.eyJ1IjoiYmpkaW9nbyIsImEiOiJjazg3bW40dnkwbjYwM2htbWc1NnBidzQ2In0.lh4trQ8-6vDRegpJWs6mBw";
    map = new mapboxgl.Map({
      container: "map",
      style: 'https://api.maptiler.com/maps/0732d48d-d1a9-444a-a472-c0a0ff8bf150/style.json?key=TLbKST4hnYUY3nc3yvDh',
      center: [-9, 38.342],
      zoom: 5,
      minZoom: 5,
      maxZoom: 8
    });

    map.on("load", function() {
      var layers = map.getStyle().layers;
      // Find the index of the first symbol layer in the map style
      var firstSymbolId;
      for (var i = 0; i < layers.length; i++) {
        if (layers[i].type === 'symbol') {
          firstSymbolId = layers[i].id;
          break;
        }
      }
      // Add source for PORTUGAL
      map.addSource("country", {
        type: "geojson",
        data: "./portugal.geo.json",
        generateId: true
      });
      map.addLayer({
        id: "country-fills",
        type: "fill",
        source: "country",
        layout: {},
        paint: {
          "fill-color":"#38393F"
        }
      });


      // Add source for CONSELHOS
      map.addSource("conselhos", {
        type: "geojson",
        data: "./map.geojson",
        generateId: true
      });

      map.addLayer({
        id: "conselhos-fills",
        type: "fill",
        source: "conselhos",
        layout: {},
        paint: {
          "fill-color": [
            "match", ["feature-state", "severity"], 
            'Bad',
            '#FF4E34',
            'Medium',
            '#FFC831',
            '#40C0A5'
          ],
          "fill-opacity": [
            "case",
            ["boolean", ["feature-state", "select"], false],
            0.4,
            0.1,
          ]
        },
        firstSymbolId
      });

      map.addLayer({
            'id': 'conselhos-borders',
            'type': 'line',
            'source': 'conselhos',
            'layout': {},
            'paint': {
                'line-width': [
                  "case",
                  ["boolean", ["feature-state", "select"], false],
                  3,
                  1,
                  
                ],
                'line-color': [
                  "case",
                  ["boolean", ["feature-state", "select"], false],
                  "#E1E1EE",
                  "#18181A"

                ]
            },
            firstSymbolId
      });
      map.on("mousemove", "conselhos-fills", function(e) {
        map.getCanvas().style.cursor = "pointer";
        if (e.features.length > 0) {
          if (hoveresConselho) {
            map.setFeatureState(
              { source: "conselhos", id: hoveresConselho },
              { hover: false }
            );
          }
          hoveresConselho = e.features[0].id;
          map.setFeatureState(
            { source: "conselhos", id: hoveresConselho },
            { hover: true }
          );
          
        }
      });

      map.on("mouseleave", "conselhos-fills", function() {
        if (hoveresConselho) {
          map.setFeatureState(
            { source: "conselhos", id: hoveresConselho },
            { hover: false }
          );
        }
        hoveresConselho = null;
      });
      
      var popup = new mapboxgl.Popup({
        closeButton: false,
        closeOnClick: false
      });
      map.on("click", "conselhos-fills", e => {
         if (e.features.length > 0) {
           
          if (selectedConselho) {
            map.setFeatureState(
              { source: "conselhos", id: selectedConselho },
              { select: false }
            );
          }
          if(selectedConselho !== e.features[0].id){
            selectedConselho = e.features[0].id;
            map.setFeatureState(
              { source: "conselhos", id: selectedConselho },
              { select: true }
            );

            Municip = e.features[0].properties.Municipality;

            var bound = L.latLngBounds(L.geoJson(e.features[0]).getBounds());
            var center_country = bound.getCenter();

            let cc = 0;
            if (
              concelhoData.filter(
                c =>
                  e.features[0].properties.Municipality.toUpperCase() ===
                  c.attributes.Concelho
              )[0]
            )
              cc = concelhoData.filter(
                c =>
                  e.features[0].properties.Municipality.toUpperCase() ===
                  c.attributes.Concelho
              )[0].attributes.ConfirmadosAcumulado_Conc;

            var coordinates = center_country;
            var description =
              "<strong>" +
              e.features[0].properties.Municipality +
              "</strong><p>" +
              cc +
              " Cases</p>";

            // Ensure that if the map is zoomed out such that multiple
            // copies of the feature are visible, the popup appears
            // over the copy being pointed to.
            while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
              coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
            }

            // Populate the popup and set its coordinates
            // based on the feature found.
            popup
              .setLngLat(coordinates)
              .setHTML(description)
              .addTo(map);
          }else{
            selectedConselho="";
            popup.remove();
          }
        
        
         }
      });

    });
  });

  function inside(y, x, vs) {
    // ray-casting algorithm based on
    // http://www.ecse.rpi.edu/Homepages/wrf/Research/Short_Notes/pnpoly.html
    var inside = false;
    if (vs.length > 0) {
      for (var i = 0, j = vs.length - 1; i < vs.length; j = i++) {
        var xi = vs[i][0],
          yi = vs[i][1];
        var xj = vs[j][0],
          yj = vs[j][1];
        var intersect =
          yi > y != yj > y && x < ((xj - xi) * (y - yi)) / (yj - yi) + xi;
        if (intersect) inside = !inside;
      }
    }
    return inside;
  }
  function placeAllCircles() {
    if (!concelhoData) return;
    let first_run = false;
    map.on("idle", function() {
      if (!first_run) {
        var features = map.queryRenderedFeatures({
          layers: ["conselhos-fills"]
        });

        var points_coord = {
          type: "FeatureCollection",
          features: []
        };

        for (var r = 0; r < features.length; r++) {
          if (
            concelhoData.filter(
              e =>
                features[r].properties.Municipality.toUpperCase() ===
                e.attributes.Concelho
            )[0]
          ) {

            
            var bound = L.latLngBounds(L.geoJson(features[r]).getBounds());
            var center_country = bound.getCenter();
            var x_max = bound.getEast();
            var x_min = bound.getWest();
            var y_max = bound.getSouth();
            var y_min = bound.getNorth();
            var j = 0;

            var num_circles =
              concelhoData.filter(
                e =>
                  features[r].properties.Municipality.toUpperCase() ===
                  e.attributes.Concelho
              )[0].attributes.ConfirmadosAcumulado_Conc / 10;

            if(num_circles>10){
              var r_severity="Bad";
            }else{
              var r_severity="Medium";

            }
            map.setFeatureState(
              { source: "conselhos", id: features[r].id },
              { severity: r_severity }
            );
            while (j < num_circles) {
              //random positions
              var lat = y_min + Math.random() * (y_max - y_min);
              var lng = x_min + Math.random() * (x_max - x_min);
              var point_pos = L.latLng(lat, lng);
              // console.log(features[r].geometry);

              if (features[r].geometry.type == "MultiPolygon") {
                for (
                  var m = 0;
                  m < features[r].geometry.coordinates.length;
                  m++
                ) {
                  if (
                    inside(lat, lng, features[r].geometry.coordinates[m][0])
                  ) {
                    // Coordinates are initially set to origin.
                    var point = {
                      type: "Feature",
                      properties: {},
                      geometry: {
                        type: "Point",
                        coordinates: [lng, lat]
                      }
                    };

                    points_coord.features.push(point);
                    j++;
                  }
                }
              } else {
                if (inside(lat, lng, features[r].geometry.coordinates[0])) {
                  // Coordinates are initially set to origin.
                  var point = {
                    type: "Feature",
                    properties: {},
                    geometry: {
                      type: "Point",
                      coordinates: [lng, lat]
                    }
                  };

                  points_coord.features.push(point);
                  j++;
                }
              }
            }
          }
        }

        map.addSource("source-points", {
          type: "geojson",
          data: points_coord
        });

        map.addLayer({
          id: "infected",
          type: "circle",
          source: "source-points",
          paint: {
            // make circles larger as the user zooms from z12 to z22
            "circle-radius": {
              base: 1.75,
              stops: [[12, 2], [22, 180]]
            },
            // color circles by ethnicity, using a match expression
            "circle-color": "#fbb03b"
          }
        });
        first_run = true;
      }
    });
  }

  function getMunicip() {
    console.info(Municip);
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
