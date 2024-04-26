# Session 5: functions, async/fetch/await und data.gv.at Wien

## 1. Neues Repo wien erstellen, clonen und online bringen

- <https://github.com/webmapping>: Create a new repository "wien"

- clone <https://github.com/webmapping/wien.git> in VS Code lokal

  - F1 Git: Clone
  - Desktop -> neues Verzeichnis "wien" -> "Select Repository Location" -> Open in New Window

- Template <https://webmapping.github.io/templates/template-wien.zip> auspacken und Inhalt ins `wien`-Verzeichnis verschieben

- alles hinzufügen und und Push zurück auf <https://github.com/webmapping/wien>

  - bei den drei Punkten oder über F1
  - zustimmen, dass oath verwendet wird
  - <https://github.com/webmapping/wien> neu laden - wunderbar

  COMMIT: <https://github.com/webmapping/wien/commit/0af9532846bc9d963882ffcdbd0c4265d0cb6c9c>

- online verfügbar machen - der Weg 2 nach **username.github.io**

  - Settings -> Pages -> Source -> "main" wählen und "Save"

  - Your site is ready to be published at <https://webmapping.github.io/wien/>

  - warten, es wird irgendwann verfügbar sein

  - in der Zwischenzeit bauen wir an der Seite weiter

## 2. Template analysieren - was passiert in main.js

- Blick auf den Stephansdom (aus Wikipedia-Koordinate) vorbereiten

  ```javascript
  // Koordinaten und Titel für den Stephansdom
  let stephansdom = {
    lat: 48.208493,
    lng: 16.373118,
    title: "Stephansdom",
  };
  ```

- Karte initialisieren, auf Stephansdom blicken

  ```javascript
  // Karte initialisieren
  let map = L.map("map", {
    center: [stephansdom.lat, stephansdom.lng],
    zoom: 12,
  });
  ```

- Startlayer _basemap.at Grau_ über _Leaflet providers_ definieren

  - Github <https://github.com/leaflet-extras/leaflet-providers>

  - Demo <http://leaflet-extras.github.io/leaflet-providers/preview/index.html>

  - in `index.html` Skript von <https://cdnjs.com/libraries/leaflet-providers> einbinden

  - in `main.js`

```javascript
// BasemapAT Layer mit Leaflet provider plugin als startLayer Variable
let startLayer = L.tileLayer.provider("BasemapAT.grau");
startLayer.addTo(map);
```

- L.control erklären

  - vorhandene Controls: Maßstab, zoom
  - jetzt neu für Layers: Objekt wird übergeben
  - verfügbare _basemap.at_ Layer hinzufügen

    - `startLayer` als ersten Eintrag "wiederverwenden", damit toggle funktioniert
    - `L.tileLayer` für einzelne Layer

```javascript
// Layer control mit Basemap.at layern über Leaflet.providers hinzufügen
L.control
  .layers({
    "BasemapAT Grau": startLayer,
    "BasemapAT Standard": L.tileLayer.provider("BasemapAT.basemap"),
    "BasemapAT High-DPI": L.tileLayer.provider("BasemapAT.highdpi"),
    "BasemapAT Gelände": L.tileLayer.provider("BasemapAT.terrain"),
    "BasemapAT Oberfläche": L.tileLayer.provider("BasemapAT.surface"),
    "BasemapAT Orthofoto": L.tileLayer.provider("BasemapAT.orthofoto"),
    "BasemapAT Beschriftung": L.tileLayer.provider("BasemapAT.overlay"),
  })
  .addTo(map);
```

- Marker für Stephansdom hinzufügen

```javascript
// Marker für den Stephansdom mit Popup zum Stephansdom Overlay hinzufügen
L.marker([stephansdom.lat, stephansdom.lng])
  .addTo(map)
  .bindPopup(stephansdom.title)
  .openPopup();
```

- Aufgabe: einen weiteren Layer über Leaflet.providers implementieren

  COMMIT: <https://github.com/webmapping/wien/commit/6bdc3b24cafef231d2cb053b225ccec29985010f>

## 3. Interface verfeinern

- Fullscreen control implementieren

  - **Achtung**: die _mapbox_ control verwenden, weil die Version von _brunob_ mit einer alten _Font awesome_ Version für das Icon, das nicht angezeigt wird, arbeitet

  - Quelle <https://github.com/Leaflet/Leaflet.fullscreen>

  - in `index.html` Skript/Stil von <https://github.com/Leaflet/Leaflet.fullscreen> (Mapbox API) einbinden

  - in `main.js`

    ```javascript
    // Fullscreen control hinzufügen
    L.control.fullscreen().addTo(map);
    ```

  COMMIT: <https://github.com/webmapping/wien/commit/85977be2f0ab70a67ab4f2925953a3b1dd1c2e41>

## 4. Daten via L.geoJSON von URL laden

- L.geoJSON aus dem letzten Kurs mit _statischem_ JSON Objekt, jetzt wollen wir Daten aus dem Netz laden

### Ein Real World Beispiel: Sehenswürdigkeiten Wien

- über die suche _Sehens_ finden wir den Datensatz **Sehenswürdigkeiten Standorte Wien** bei <https://data.gv.at>, dem OpenData Portal von Österreich

- auf der Katalog Seite der Sehenswürdigkeiten finden wir den Link zum GeoJSON bei **WFS GetFeature (JSON) JSON**

- über **Mehr Information** landen wir auf der Detailseite mit der gewünschten [URL](https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:SEHENSWUERDIGOGD&srsName=EPSG:4326&outputFormat=json)

- wir sehen uns die Datei `SEHENSWUERDIGOGD.json` in VS Code an und verwenden natürlich **F1 format file** damit wir die Struktur besser erkennen

- wir erkennen, dass es sich um eine Sammlung von _Features_ handelt, wie viele es insgesamt sind und in welcher Projektion sie vorliegen

  ```json
  {
    "type": "FeatureCollection",
    "totalFeatures": 63,
    "features": [],
    "crs": {
      "type": "name",
      "properties": {
        "name": "urn:ogc:def:crs:EPSG::4326"
      }
    }
  }
  ```

- die einzelnen _Features_ sind im `features`-Array zu finden - dessen erster Eintrag ist wiederum ein Javascript-Objekt

  ```json
  {
    "type": "Feature",
    "id": "SEHENSWUERDIGOGD.411009",
    "geometry": {
      "type": "Point",
      "coordinates": [16.373675865880358, 48.210760467880625]
    },
    "geometry_name": "SHAPE",
    "properties": {
      "OBJECTID": 411009,
      "NAME": "Ankeruhr",
      "ADRESSE": "Hoher Markt 10/11",
      "WEITERE_INF": "http://www.wien.info/article.asp?IDArticle=2018",
      "THUMBNAIL": "https://www.wien.gv.at/viennagis/bubble/ankeruhr.jpg",
      "SE_ANNO_CAD_DATA": null
    }
  }
  ```

  - wir erkennen die Geometrie in `geometry` und die Attribute in `properties`

  - Leaflet versteht das GeoJSON-Format und kann es automatisch in Marker, Linien und Flächen umwandeln. Dazu müssen wir die Daten aber zuerst laden und das geht mit Hilfe einer Funktion, die noch dazu asynchron ausgeführt wird - wir müssen uns also zuerst noch kurz mit Funktionen beschäftigen

## Sidestep: Javascript Funktionen

Eine Funktion ist vereinfacht gesagt ein Codeblock mit einem Namen, der "irgend etwas tut" wenn wir ihn aufrufen. Beim Aufruf können wir Parameter für "das Tun" innerhalb der Funktion mitschicken und wenn der Codeblock abgearbeitet ist, kann die Funktion auch wieder einen einzelnen Wert, einen Array oder eine Objekt zurückliefern.

- eine einfach Funktion zum Addieren von zwei Zahlen sieht damit so aus:

  ```javascript
  function addiere (zahl1, zahl2) {
      let summe = zahl1 + zahl2;
      return summe;
  }
  ```

- und so wird die Funktion `addiere` aufgerufen

  ```javascript
  let ergebnis = addiere(2, 4);
  console.log(ergebnis);
  ```

- genau so eine Funktion werden wir jetzt schreiben, um die GeoJSON-Daten der Sehenswürdigkeiten zu laden

### b) GeoJSON Daten asynchron mit einer Funktion laden

- so sieht die Funktion aus :-o

  ```javascript
  // Sehenswürdigkeiten laden
  async function loadSights(url) {
    let response = await fetch(url);
    let geojson = await response.json();
    console.log(geojson);
  }
  loadSights(
    "https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:SEHENSWUERDIGOGD&srsName=EPSG:4326&outputFormat=json",
  );
  ```

- was passiert dabei?

  - in der ersten Zeile wird eine asynchrone (`async`) Funktion (`function`) mit dem Namen `loadSights` definiert, die als Parameter eine Internetadresse (`url`) erwartet

  - der Codeblock der Funktion wird durch geschwungene Klammern (`{}`) abgegrenzt

  - in der Funktion wird im ersten Schritt auf die Antwort (`response`) des Servers gewartet (`wait`) die er beim Aufruf (`fetch`) der Internetadresse (`url`) zurückliefert

  - ist die Antwort angekommen, kann im zweiten Schritt das GeoJSON-Objekt der Antwort (`response.json`) in der Variablen `geojson` gespeichert werden. Auch das kann dauern, deshalb warten wir wieder, bis es soweit ist (`wait`)

  - **F12** zeigt uns schließlich in der Konsole das geladenen GeoJSON-Objekt

  - **Wichtig**: die Funktion wird natürlich nur ausgeführt, wenn wir sie aufrufen (`loadSights("https://...")` und dabei die Internetadresse des gewünschten GeoJSON Files übergeben - diese "landet" in der Funktion dann als Variable `url`

### c) GeoJSON Daten mit L.geoJSON visualisieren

Zum Visualisieren benötigen wir noch eine Zeile Code, wir verwenden die Leaflet Methode [L.geoJSON](https://leafletjs.com/reference.html#geojson), die wir in der Leaflet Dokumentation unter **Other Layers / GeoJSON** finden. Im Abschnitt **Factory** sehen wir, dass wir das geojson-Objekt als ersten Parameter übergeben können, die Optionen brauchen wir vorerst nicht. Wie schon bei früheren Layern verwenden wir `.addTo(map)` um den geoJSON Layer an die Karte zu hängen.

```javascript
async function loadSights(url) {
  let response = await fetch(url);
  let geojson = await response.json();
  console.log(geojson);

  L.geoJSON(geojson).addTo(map);
}
```

  COMMIT: <https://github.com/webmapping/wien/commit/1bea70768e6f4f41ecaa5ae0add79db51686272f>


die Marker für die Sehenswürdigkeiten sind damit auf der Karte sichtbar

## GeoJSON-Layer der Sehenswürdigkeiten in ein Overlay schreiben

Noch eleganter ist es, den GeoJSON-Layer in ein eigenes Overlay zu schreiben.

- zuerst definieren wir ein Overlay-Objekt `themaLayer` mit einem Key `sights` und als Value eine [L.featureGroup()](https://leafletjs.com/reference.html#featuregroup). Damit der Layer später gleich angezeigt wird, hängen wir ihn mit `.addTo(map)` an die Karte.

    ```javascript
    let themaLayer = {
        sights: L.featureGroup().addTo(map),
    }
    ```

- dann fügen wir diesen thematischen Layer als neues Objekt mit einem Label *Sehenswürdigkeiten* zur Layercontrol der Karte hinzu. Wir erweitern also einfach den L.control.layers Aufruf

    ```javascript
    L.control.layers({
        // Hintergrundlayer
    }, {
        "Sehenswürdigkeiten": themaLayer.sights,
    });
    ```

- zum Schluss hängen wir unsere Punkte nicht an `map`  sondern unser Overlay `themaLayer.sights`

    ```javascript
    L.geoJSON(geojson).addTo(themaLayer.sights);
    ```

COMMIT: <https://github.com/webmapping/wien/commit/0a2789d1e9e0261df91f146467ad3f0a3dbacbcf>

Damit kann der Sehenswürdigkeiten Layer ein- und ausgeschaltet werden

## Layer Sehenswürdigkeiten verfeinern und Popup hinzufügen

* wir könnten, wie in der Leaflet Dokumentation von [L.geoJSON](https://leafletjs.com/reference.html#geojson) im *Usage example* gezeigt, das Popup einfach wieder vor dem `.addTo()` hinzufügen, das machen wir aber nicht, denn in einem späteren Schritt, beim Clustern von Punkten, würde uns das nur Probleme bereiten. Wir könnten auch [pointToLayer](https://leafletjs.com/reference.html#geojson-pointtolayer) für das Popup verwenden, machen wir aber auch nicht, weil Linien und Flächen keine `pointToLayer` Option haben.

* Deshalb verwenden wir [onEachFeature](https://leafletjs.com/reference.html#geojson-oneachfeature) - es wird für jedes Element des GeoJSON-Objekts einmal ausgeführt und erwartet eine Funktion, der es das GeoJSON-Objekt in `feature` und den Leafletmarker in `layer` übergibt. wir speichern uns eine "Abkürzung" zu den Attributen der einzelnen Marker (`feature.properties`) in einer Variablen `prop`. Das Popup definieren wir als **Template string** mit *Backticks*, verwenden die Attribute `NAME`, `ADRESSE`, `THUMBNAIL` sowie `WEITERE_INF` und hängen es mit `bindPopup` an den Marker, den wir in `layer` finden

    ```javascript
    L.geoJSON(geojson, {
      onEachFeature: function (feature, layer) {
        console.log(feature);
        layer.bindPopup(`
          <img src="${feature.properties.THUMBNAIL}" alt="*">
          <h4><a href="${feature.properties.WEITERE_INF}" target="wien">${feature.properties.NAME}</a></h4>
          <address>${feature.properties.ADRESSE}</address>
        `);
      }
    }).addTo(themaLayer.sights);
    ```

    COMMIT: <https://github.com/webmapping/wien/commit/71be53fe19c975c371e6b5057e78dbe2998a9b86>

    * `alt="*"` bedeutet, dass unser Bild rein dekorativ ist. Wir wissen ja auch gar nicht, was auf den einzelnen Bildern zu sehen ist.
    
    * `target="Wien"` bewirkt, dass alle Weblinks im selben Tab geöffnet werden

## Übungsaufgabe: wir erstellen vier weitere Layer

Über die Suche bei <https://www.data.gv.at/> suchen wir Layer mit JSON-Daten und implementieren sie mit den jeweiligen Vorgaben:

1. Funktion `loadLines`
    * Suche: *sightseeing* - <https://www.data.gv.at/suche/?searchterm=sightseeing>
    * Datensatz *Touristische Kraftfahrlinien Liniennetz Vienna Sightseeing Linie Wien*
    * Overlay Key/Label: `lines` und "Liniennetz Vienna Sightseeing"

    COMMIT: <https://github.com/webmapping/wien/commit/be14b7d6ea560efb314052afb5fd30d9788e5bb9>

2. Funktion `loadStops`
    * Suche: *sightseeing* - <https://www.data.gv.at/suche/?searchterm=sightseeing>
    * Datensatz *Touristische Kraftfahrlinien Haltestellen Vienna Sightseeing Linie Standorte Wien*
    * Overlay Key/Label: `stops` und "Haltestellen Vienna Sightseeing"

    COMMIT: https://github.com/webmapping/wien/commit/8040bb895b60ab961a33baaf0683bc89cddbad22>

3. Funktion `loadZones`
    * Suche: *Fußgängerzonen* - <https://www.data.gv.at/suche/?searchterm=Fu%C3%9Fg%C3%A4ngerzonen>
    * Datensatz *Fußgängerzonen Wien*
    * Overlay Key/Label: `zones` und "Fußgängerzonen"

    COMMIT: <https://github.com/webmapping/wien/commit/ef42d26b160d9955ba27df70ad96479b98eb0504>

4. Funktion `loadHotels`
    * Suche: *Hotels*
    * Datensatz *Hotels und Unterkünfte Standorte Wien*
    * Overlay Key/Label: `hotels` und "Hotels und Unterkünfte"

    COMMIT: <https://github.com/webmapping/wien/commit/c1d53fb96c40b74c1cd57b417318b6cda5df157e>

**das war's für heute ...**