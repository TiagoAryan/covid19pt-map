<script>
  import { onMount } from "svelte";

  export let mapa;
  export let timelineData;
  export let concelhoData;
  export let mapboxgl;
  var type = false;
  var map;

  let Municip;

  $: map && concelhoData, playMap();
  $: Municip && getMunicip();

  onMount(() => {
    let mouseState=false;
    let selectedConselho;
    let hoveresConselho;
    let centerMap, zoomMap, bounds;
    if (mapa == "main") {
      centerMap = [-7.8, 39.5];
      zoomMap = 6;
      bounds = [[-13, 36.9469], [-3, 42.1789]];

    } else if (mapa == "acores") {
      centerMap = [-28.1, 38.5];
      zoomMap = 5;
      bounds = [[-31.57, 35], [-24.52, 41]];
    } else if (mapa == "madeira") {
      centerMap = [-16.8, 32.7];
      zoomMap = 8;
      bounds = [[-17.5087, 32.187], [-16.0311, 33.3348]];
    }
    
    map = new mapboxgl.Map({
      container: mapa,
      style: 'https://api.maptiler.com/maps/0732d48d-d1a9-444a-a472-c0a0ff8bf150/style.json?key=TLbKST4hnYUY3nc3yvDh',
      center: centerMap,
      zoom: zoomMap,
      minZoom: 5,
      maxZoom: 8,
      maxBounds: bounds
    });
    map.fitBounds(bounds);
   
    map.on("load", function() {
      var layers = map.getStyle().layers;
      // Find the index of the first symbol layer in the map style
      var firstSymbolId;
      for (var i = 0; i < layers.length; i++) {
        if (layers[i].type === "symbol") {
          firstSymbolId = layers[i].id;
          break;
        }
      }
      // Add source for PORTUGAL
      map.addSource("country", {
        type: "geojson",
        data: "./portugal.geojson",
        generateId: true
      });
      map.addLayer({
        id: "country-fills",
        type: "fill",
        source: "country",
        layout: {},
        paint: {
          "fill-color": "#38393F"
        }
      });

      // Add source for REGIOES
      map.addSource("regioes", {
        type: "geojson",
        data: "./regioes.geojson",
        generateId: true
      });
      // Add layer fill for REGIOES
      map.addLayer({
        id: "regioes-fills",
        type: "fill",
        source: "regioes",
        layout: {
          visibility: "visible"
        },
        paint: {
          "fill-color": [
            "match",
            ["feature-state", "severity"],
            "Bad",
            "#FF4E34",
            "Medium",
            "#FFC831",
            "#40C0A5"
          ],
          "fill-opacity": [
            "case",
            ["boolean", ["feature-state", "select"], false],
            0.4,
            0.1
          ]
        },
        firstSymbolId
      });
      // Add layer line for REGIOES
      map.addLayer({
        id: "regioes-borders",
        type: "line",
        source: "regioes",
        layout: {
          visibility: "visible"
        },
        paint: {
          "line-width": [
            "case",
            ["boolean", ["feature-state", "select"], false],
            3,
            1
          ],
          "line-color": [
            "case",
            ["boolean", ["feature-state", "select"], false],
            "#E1E1EE",
            "#18181A"
          ]
        },
        firstSymbolId
      });

      // Add source for CONSELHOS
      map.addSource("conselhos", {
        type: "geojson",
        data: "./municip.geojson",
        generateId: true
      });
      // Add layer fill for CONSELHOS
      map.addLayer({
        id: "conselhos-fills",
        type: "fill",
        source: "conselhos",
        layout: {
          visibility: "none"
        },
        paint: {
          "fill-color": [
            "match",
            ["feature-state", "severity"],
            "Bad",
            "#FF4E34",
            "Medium",
            "#FFC831",
            "#40C0A5"
          ],
          "fill-opacity": [
            "case",
            ["boolean", ["feature-state", "select"], false],
            0.4,
            0.1
          ]
        },
        firstSymbolId
      });
      // Add layer line for CONSELHOS
      map.addLayer({
        id: "conselhos-borders",
        type: "line",
        source: "conselhos",
        layout: {
          visibility: "none"
        },
        paint: {
          "line-width": [
            "case",
            ["boolean", ["feature-state", "select"], false],
            3,
            1
          ],
          "line-color": [
            "case",
            ["boolean", ["feature-state", "select"], false],
            "#E1E1EE",
            "#18181A"
          ]
        },
        firstSymbolId
      });

      map.on("mousemove", "conselhos-fills", function(e) {
        mouseState=true;
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
        mouseState=false;
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
       map.on("click", function() {
        if(!mouseState){
          if (selectedConselho) {
            map.setFeatureState(
              { source: "conselhos", id: selectedConselho },
              { select: false }
            );
          }
          selectedConselho = "";
          popup.remove();
        }
      })
      map.on("click", "conselhos-fills", e => {
        if (e.features.length > 0) {
          if (selectedConselho) {
            map.setFeatureState(
              { source: "conselhos", id: selectedConselho },
              { select: false }
            );
          }
          if (selectedConselho !== e.features[0].id) {
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
          } else {
            selectedConselho = "";
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

  function playMap() {
    if (!timelineData && !concelhoData) return;

    let length = Object.keys(timelineData.data).length;
    var interval;
    let run = true;
    let day = 0;

    map.on("idle", function() {
      if (run)
        interval = setInterval(() => {
          if (map.getLayer("infected")) map.removeLayer("infected");
          if (map.getSource("source-points")) map.removeSource("source-points");

          placeAllCircles(day);

          day++;
          if (day >= length) {
            clearInterval(interval);
            map.setLayoutProperty("regioes-fills", "visibility", "none");
            map.setLayoutProperty("regioes-borders", "visibility", "none");
            map.removeLayer("infected");
            map.removeSource("source-points");
            map.setLayoutProperty("conselhos-fills", "visibility", "visible");
            map.setLayoutProperty("conselhos-borders", "visibility", "visible");
            placeConcelhoCircles();
          }
        }, 300);
      run = false;
    });
  }

  function placeAllCircles(day) {
    var features = map.queryRenderedFeatures({
      layers: ["regioes-fills"]
    });

    var points_coord = {
      type: "FeatureCollection",
      features: []
    };

    for (var r = 0; r < features.length; r++) {
      let num;
      if (features[r].properties.NAME === "Açores")
        num = timelineData.confirmados_acores[day];
      else if (features[r].properties.NAME === "Alentejo")
        num = timelineData.confirmados_arsalentejo[day];
      else if (features[r].properties.NAME === "Algarve")
        num = timelineData.confirmados_arsalgarve[day];
      else if (features[r].properties.NAME === "Centro")
        num = timelineData.confirmados_arscentro[day];
      else if (features[r].properties.NAME === "Lisboa")
        num = timelineData.confirmados_arslvt[day];
      else if (features[r].properties.NAME === "Norte")
        num = timelineData.confirmados_arsnorte[day];
      else if (features[r].properties.NAME === "Madeira")
        num = timelineData.confirmados_madeira[day];

      if (num) {
        var bound = L.latLngBounds(L.geoJson(features[r]).getBounds());
        var center_country = bound.getCenter();
        var x_max = bound.getEast();
        var x_min = bound.getWest();
        var y_max = bound.getSouth();
        var y_min = bound.getNorth();
        var j = 0;

        var num_circles = num / 25;
        if (num_circles > 10) {
          var r_severity = "Bad";
        } else {
          var r_severity = "Medium";
        }
        map.setFeatureState(
          { source: "regioes", id: features[r].id },
          { severity: r_severity }
        );
        while (j < num_circles) {
          //random positions
          var lat = y_min + Math.random() * (y_max - y_min);
          var lng = x_min + Math.random() * (x_max - x_min);
          var point_pos = L.latLng(lat, lng);

          if (features[r].geometry.type == "MultiPolygon") {
            for (var m = 0; m < features[r].geometry.coordinates.length; m++) {
              if (inside(lat, lng, features[r].geometry.coordinates[m][0])) {
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
          base: 2.5,
          stops: [[12, 2], [22, 180]]
        },
        // color circles by ethnicity, using a match expression
        "circle-color": "#fbb03b"
      }
    });
  }

  function placeConcelhoCircles() {
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

            if (num_circles > 10) {
              var r_severity = "Bad";
            } else {
              var r_severity = "Medium";
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
  div {
    height: 100%;
  }
</style>

<div id={mapa} />
