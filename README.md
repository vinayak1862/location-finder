# location-finder
Lattitude and longitude location finder using map api
<!DOCTYPE html>
<html>
<head>
  <title>Smart Location Finder</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />

  <style>
    body {
      font-family: Arial;
      padding: 10px;
    }
    input, button {
      width: 100%;
      padding: 8px;
      margin: 5px 0;
    }
    #map {
      height: 300px;
      margin-top: 10px;
    }
    #info {
      margin-top: 8px;
      font-weight: bold;
    }
  </style>
</head>

<body>

  <h3>Smart Location Finder (Satellite + Name)</h3>

  <input type="number" step="any" id="lat" placeholder="Enter Latitude">
  <input type="number" step="any" id="lon" placeholder="Enter Longitude">

  <button onclick="showLocation()">Show on Map</button>
  <button onclick="loadCollege()">📍 College of Engineering Perumon</button>

  <div id="info"></div>
  <div id="map"></div>

  <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

  <script>
    var normalMap = L.tileLayer(
      'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png'
    );

    var satelliteMap = L.tileLayer(
      'https://server.arcgisonline.com/ArcGIS/rest/services/' +
      'World_Imagery/MapServer/tile/{z}/{y}/{x}'
    );

    var map = L.map('map', {
      center: [0, 0],
      zoom: 2,
      layers: [satelliteMap]
    });

    L.control.layers({
      "Normal Map": normalMap,
      "Satellite View": satelliteMap
    }).addTo(map);

    var marker;

    function getDirection(lat, lon) {
      let latDir = lat >= 0 ? "N" : "S";
      let lonDir = lon >= 0 ? "E" : "W";
      return Math.abs(lat) + "° " + latDir + ", " + Math.abs(lon) + "° " + lonDir;
    }

    function showLocation() {
      var lat = parseFloat(document.getElementById("lat").value);
      var lon = parseFloat(document.getElementById("lon").value);

      if (isNaN(lat) || isNaN(lon) || lat < -90 || lat > 90 || lon < -180 || lon > 180) {
        alert("Enter valid latitude (-90 to 90) and longitude (-180 to 180)");
        return;
      }

      if (marker) map.removeLayer(marker);

      marker = L.marker([lat, lon]).addTo(map);
      map.setView([lat, lon], 17);

      document.getElementById("info").innerText =
        "Coordinates: " + getDirection(lat, lon) + "\nFetching location name...";

      // Reverse Geocoding
      fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lon}`)
        .then(response => response.json())
        .then(data => {
          document.getElementById("info").innerText =
            "📍 Location: " + data.display_name +
            "\n📐 Coordinates: " + getDirection(lat, lon);
        });
    }

    function loadCollege() {
      document.getElementById("lat").value = 8.964889;
      document.getElementById("lon").value = 76.620084;
      showLocation();
    }
  </script>

</body>
</html>
