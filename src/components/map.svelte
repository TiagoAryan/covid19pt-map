<script>
  import { onMount } from "svelte";

  export let timelineData;
  export let concelhoData;
  export let type = true;

  $: concelhoData, placeAllCircles();

  onMount(() => {
    var bounds = [[36, -6], [42, -9]];
    var selectedConselho;
    var first_run=false;

    mapboxgl.setRTLTextPlugin('https://cdn.maptiler.com/mapbox-gl-js/plugins/mapbox-gl-rtl-text/v0.1.2/mapbox-gl-rtl-text.js');
    mapboxgl.accessToken='pk.eyJ1IjoiYmpkaW9nbyIsImEiOiJjazg3bW40dnkwbjYwM2htbWc1NnBidzQ2In0.lh4trQ8-6vDRegpJWs6mBw';
    var map = new mapboxgl.Map({
      container: 'map',
      style: 'https://api.maptiler.com/maps/5ce0b2a2-d5dc-44ae-84f3-7211439b9474/style.json?key=TLbKST4hnYUY3nc3yvDh',
      center: [-8, 38],
      zoom: 6,
      minZoom: 6,
      maxZoom: 8
    });
    
  map.on('load', function() {
    var layers = map.getStyle().layers;
    // Find the index of the first symbol layer in the map style
    var firstSymbolId;
    for (var i = 0; i < layers.length; i++) {
      if (layers[i].type === 'symbol') {
        firstSymbolId = layers[i].id;
      break;
      }
    }
      // Add source for admin-1 Boundaries
      map.addSource('conselhos', {
              'type': 'geojson',
              'data':  './mapp.geojson',
              'generateId': true 
          });
        map.addLayer({
            'id': 'conselhos-fills',
            'type': 'fill',
            'source': 'conselhos',
            'layout': {},
            'paint': {
                'fill-color': '#EFEFF6',
                'fill-opacity': [
                    'case',
                    ['boolean', ['feature-state', 'hover'], false],
                    0.2,
                    0
                ]
            }
        });

          map.addLayer({
            'id': 'conselhos-lines',
            'type': 'line',
            'source': 'conselhos',
            'layout': {},
            'paint': {
                'line-width': [
                    'case',
                    ['boolean', ['feature-state', 'hover'], false],
                    3,
                    1
                ],
                'line-color': [
                    'case',
                    ['boolean', ['feature-state', 'hover'], false],
                    '#EFEFF6',
                    '#18181A'
                ]
            }
        });
         map.on('click', 'conselhos-fills', function(e) {
            if (e.features.length > 0) {
                //var features = map.queryRenderedFeatures({ layers: ['conselhos-fills'] });
                //var uniqueFeatures = getUniqueFeatures(features, 'cartodb_id');
                if (selectedConselho) {
                    map.setFeatureState(
                        { source: 'conselhos',  id: selectedConselho },
                        { hover: false }
                    );
                }
                selectedConselho = e.features[0].id;
                map.setFeatureState(
                    { source: 'conselhos', id: selectedConselho },
                    { hover: true }
                );
            }
        });

        function getUniqueFeatures(array, comparatorProperty) {
          var existingFeatureKeys = {};
          // Because features come from tiled vector data, feature geometries may be split
          // or duplicated across tile boundaries and, as a result, features may appear
          // multiple times in query results.
          var uniqueFeatures = array.filter(function(el) {
          if (existingFeatureKeys[el.properties[comparatorProperty]]) {
          return false;
          } else {
          existingFeatureKeys[el.properties[comparatorProperty]] = true;
          return true;
          }
          });
          
        return uniqueFeatures;
        }

      });

  map.on('idle',function(){
    console.log("idle");
    //your code here 
    if(!first_run){

      var features = map.queryRenderedFeatures({ layers: ['conselhos-fills'] });
      var num_circles = 10;
       var points_coord = {
              type: "FeatureCollection",
              features: []
        };

      for (var r = 0; r < features.length; r++) {
        var bound = L.latLngBounds(L.geoJson(features[r]).getBounds());
        var center_country = bound.getCenter();
        var x_max = bound.getEast();
        var x_min = bound.getWest();
        var y_max = bound.getSouth();
        var y_min = bound.getNorth();
        var j = 0;
        
        while (j < num_circles) {
          //random positions
          var lat = y_min + Math.random() * (y_max - y_min);
          var lng = x_min + Math.random() * (x_max - x_min);
          var point_pos = L.latLng(lat, lng);
          //circle visuals
          var circleOptions = {
            color: "#ff0000",
            fillColor: "#ff0000",
            fillOpacity: 0.3,
            weight: 1,
            interactive: false
          };
          
          if ( features[r].geometry.type == "MultiPolygon") {
            for ( var m = 0;  m <  features[r].geometry.coordinates.length;m++ ) {
              if (inside(lat, lng, features[r].geometry.coordinates[m][0])) {
                
                // Coordinates are initially set to origin.
                var point = {
                      type: "Feature",
                      properties: {},
                      geometry: {
                        type: "Point",
                        coordinates: [lng, lat]
                      }
                    }
                  
                
                points_coord.features.push(point);
                j++;
              }
            }

          }else{
             if (inside(lat, lng, features[r].geometry.coordinates[0])) {
              
              // Coordinates are initially set to origin.
              var point = {
                    type: "Feature",
                    properties: {},
                    geometry: {
                      type: "Point",
                      coordinates: [lng, lat]
                    }
                  }
                
              
              points_coord.features.push(point);
              j++;
            }
          }
        }
      }
      
       map.addSource('source-points', {
          type: "geojson",
          data: points_coord
        });

        map.addLayer({
              'id': 'infected',
              'type': 'circle',
              'source': 'source-points',
              'paint': {
              // make circles larger as the user zooms from z12 to z22
              'circle-radius': {
                'base': 1.75,
                'stops': [[12, 2], [22, 180]]
                },
              // color circles by ethnicity, using a match expression
              'circle-color': '#fbb03b'
              }
        });

      
      first_run=true;
    }
    })
  
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
