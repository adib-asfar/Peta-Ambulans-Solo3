<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<HTML>
<HEAD>
	<META HTTP-EQUIV="CONTENT-TYPE" CONTENT="text/html; charset=windows-1252">
	<TITLE></TITLE>
	<META NAME="GENERATOR" CONTENT="OpenOffice 4.1.5  (Win32)">
	<META NAME="CREATED" CONTENT="0;0">
	<META NAME="CHANGED" CONTENT="0;0">
	<META NAME="" CONTENT="">
	<META NAME="viewport" CONTENT="initial-scale=1,maximum-scale=1,user-scalable=no">
</HEAD>
<BODY LANG="en-US" DIR="LTR">
<P>MIME-Version: 1.0 Content-Type: Multipart/related;
boundary=&quot;boundary&quot;;type=Text/HTML --boundary Content-Type:
text/html; Content-Location: document.mhtml 
<SCRIPT SRC="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.5.0/jquery.min.js"></SCRIPT>
<SCRIPT SRC="https://api.mapbox.com/mapbox-gl-js/v1.9.1/mapbox-gl.js"></SCRIPT><SCRIPT SRC="https://npmcdn.com/csv2geojson@latest/csv2geojson.js"></SCRIPT><SCRIPT SRC="https://npmcdn.com/@turf/turf/turf.min.js"></SCRIPT></P>
<P ALIGN=CENTER>Navigation</P>
<P ALIGN=CENTER STYLE="margin-right: 0.21in; margin-top: 0.05in; margin-bottom: 0.05in; border: none; padding: 0in">
<FONT COLOR="#ffffff"><SPAN STYLE="font-weight: normal"><SPAN STYLE="background: #4e9caf"><A HREF="Tes%20Lokasi%20-%20Copy.html">Backward</A><A HREF="Tes%20Lokasi%20-%20Copy.html">Forward</A></SPAN></SPAN></FONT></P>
<DIV ID="map" DIR="LTR">
	<P STYLE="margin-bottom: 0in"><BR>
	</P>
</DIV>
<P STYLE="margin-bottom: 0in"><SCRIPT>
    var transformRequest = (url, resourceType) => {
      var isMapboxRequest =
        url.slice(8, 22) === "api.mapbox.com" ||
        url.slice(10, 26) === "tiles.mapbox.com";
      return {
        url: isMapboxRequest
          ? url.replace("?", "?pluginName=sheetMapper&")
          : url
      };
    };
    //YOUR TURN: add your Mapbox token 
    mapboxgl.accessToken = 'pk.eyJ1IjoiYWRpYm1hc2ZhciIsImEiOiJja2hwdnZkc3kxZXY3MzhteGdkM2hoYjA0In0.D5p4SAmaOvQBfVf6pfo3sw'; //Mapbox token 
    var map = new mapboxgl.Map({
      container: 'map', // container id
      style: 'mapbox://styles/mapbox/streets-v11', //stylesheet location
      center: [110.821,-7.559], // starting position
      zoom: 10,// starting zoom
      transformRequest: transformRequest
    });

    $(document).ready(function () {
      $.ajax({
        type: "GET",
        //YOUR TURN: Replace with csv export link
        url: 'https://docs.google.com/spreadsheets/d/1GEgc64-NzwWi9nzMB6AT8rsEZAMV1rYlWB0ORec-CmM/gviz/tq?tqx=out:csv&sheet=Sheet1',
        dataType: "text",
        success: function (csvData) { makeGeoJSON(csvData); }
      });



      function makeGeoJSON(csvData) {
        csv2geojson.csv2geojson(csvData, {
          latfield: 'Latitude',
          lonfield: 'Longitude',
          delimiter: ','
        }, function (err, data) {
          map.on('load', function () {

            //Add the the layer to the map 
            map.addLayer({
              'id': 'csvData',
              'type': 'circle',
              'source': {
                'type': 'geojson',
                'data': data
              },
              'paint': {
                'circle-radius': 10,
                'circle-color': "purple"
              }
            });


            // When a click event occurs on a feature in the csvData layer, open a popup at the
            // location of the feature, with description HTML from its properties.
            map.on('click', 'csvData', function (e) {
              var coordinates = e.features[0].geometry.coordinates.slice();

              //set popup text 
              //You can adjust the values of the popup to match the headers of your CSV. 
              // For example: e.features[0].properties.Name is retrieving information from the field Name in the original CSV. 
              var description = `<h3>` + e.features[0].properties.Lembaga + `</h3>` + `<h4>` + `<b>` + `Alamat: ` + `</hhb>` + e.features[0].properties.Alamat + `</hhbhbh4>` + `<h4>` + `<b>` + `Telepon_1: ` + `</hhbhbhhbhb>` + e.features[0].properties.Telepon_1 + `</hhbhbhhbhbhbhbh4>` + `<h4>` + `<b>` + `Telepon_2: ` + `</hhbhbhhbhbhbhbhhbhbhb>` + e.features[0].properties.Telepon_2 + `</hhbhbhhbhbhbhbhhbhbhbhbhbhbh4>` + `<h4>` + `<b>` + `Fasilitas: ` + `</hhbhbhhbhbhbhbhhbhbhbhbhbhbhhbhbhbhb>` + e.features[0].properties.Fasilitas + `</hhbhbhhbhbhbhbhhbhbhbhbhbhbhhbhbhbhbhbhbhbhbh4>`;

              // Ensure that if the map is zoomed out such that multiple
              // copies of the feature are visible, the popup appears
              // over the copy being pointed to.
              while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
                coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
              }

              //add Popup to map

              new mapboxgl.Popup()
                .setLngLat(coordinates)
                .setHTML(description)
                .addTo(map);
            });

            // Change the cursor to a pointer when the mouse is over the places layer.
            map.on('mouseenter', 'csvData', function () {
              map.getCanvas().style.cursor = 'pointer';
            });

            // Change it back to a pointer when it leaves.
            map.on('mouseleave', 'places', function () {
              map.getCanvas().style.cursor = '';
            });

            var bbox = turf.bbox(data);
            map.fitBounds(bbox, { padding: 50 });

          });

        });
      };
    });




  
</SCRIPT>--boundary--</P>
</BODY>
</HTML>
