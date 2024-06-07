# Session 10: QGIS, TopoJSON, Plugins und Tips für die Projektphase

## 1. Tips für die Projektphase

- arbeitet nur im `ORGNAME.github.io` Repo

- legt keine zusätzlichen Repos an, sondern Unterverzeichnisse wenn nötig

- am Beispiel eines fiktiven Wind & Wetter Projekts könnten die Ziel-URLs so aussehen:

    ```text
    https://windundwetter.github.io/index.html
    https://windundwetter.github.io/main.css
    https://windundwetter.github.io/team.html
    https://windundwetter.github.io/wind/index.html
    https://windundwetter.github.io/wind/main.js
    https://windundwetter.github.io/wind/icons/wind.png
    https://windundwetter.github.io/wetter/index.html
    https://windundwetter.github.io/wetter/main.js
    https://windundwetter.github.io/wetter/icons/sunny.svg
    https://windundwetter.github.io/wetter/icons/rainy.svg
    https://windundwetter.github.io/wetter/lib/plugin.js
    https://windundwetter.github.io/wetter/lib/plugin.css
    ```

- verlink wird dann
    
    - einfach mit dem Namen der HTML-Seite, wenn beide Dateien im selben Verzeichnis sind: z.B. `index.html -> team.html`

        ```html
        <!-- in index.html -->
        <a href="team.html">Team-Seite</a>
        ```

    - mit dem Namen des Unterordners plus  dem Namen der HTML-Seite, wenn die Zielseite in einem Unterordner liegt: z.B. `index.html -> wetter/index.html` (optional mit `./`)

        ```html
        <!-- in index.html -->
        <a href="wetter/index.html">zur Wetterseite</a>
        <a href="./wetter/index.html">zur Wetterseite</a>
        ```

    - mit `../` wenn die Zielseite im übergeordneten Ordner liegt: z.B. `wetter/index.html -> index.html`

        ```html
        <!-- in wetter/index.html -->
        <link rel="stylesheet" href="../main.css">
        <a href="../index.html">zur Startseite</a>
        ```

- sprecht euch ab, wer, wo arbeitet

- immer **Git-Pull** bevor man zu Arbeiten beginnt

- immer **Commit + Git-Push** wenn man fertig ist

- wenn alles einheitlich aussehen soll, empfiehlt es sich, ein zentrales Stylesheet zu verwenden

- GeoJSON Daten müssen immer geographische Koordinaten als Projektion haben - EPSG:4326

- beachtet die Größe eurer Datenfiles - ab mehreren MBs wird's schwierig

    - vor allem originale GIS-Daten sind meist unbrauchbar, denn sie bestehen aus viel zu vielen Punkten pro Segment und teilweise aus viel zu vielen Attributen
        
    - generalisiert die Daten wenn nötig im GIS

    - reduziert die Nachkommastellen beim Export nach GeoJSON

    - entfernt unnötige Attribut-Spalten

    - kürzt zu lange Attribut-Namen

    - wenn gar nichts hilft, konvertiert die GeoJSON Daten nach **TopoJSON** (siehe Unten) oder verzichtet darauf und sucht eine andere Lösung

    - achtet bei GPX Daten auf den Datenschutz! Verwendet keine Daten, wenn ihr euch nicht sicher seid, dass ihr das dürft. Fragt zur Sicherheit nach, ob ihr GPX-Daten von anderen Websites verwenden könnt

    - achtet auch bei Bildern und Videos auf die Rechte

    - verkleinert zu große Bilder. Wenn die Seitenbreite im CSS mit 1200 Pixel festgelegt ist, hat es keinen Sinn, Originalbilder mit 4000x 3000 Pixel unbearbeitet einzubauen. Das verlängert unnötig die Ladezeit der Projektseite


## 2. GeoJSON Dateien aus Shapefiles in QGIS machen

- Daten downloaden

    - <https://data.gv.at> ansteuern

    - Suche: *Vorarlberg Mountainbike*

    - Mountainbike-Wegweisertafeln in Vorarlberg <https://www.data.gv.at/katalog/de/dataset/mountainbike_tafeln>

    - SHAPE-ZIP-Download und auspacken

- Einlesen in QGIS

    - QGIS starten

    - "*Layer / Layer hinzufügen / Vekorlayer hinzufügen*"

    - Kodierung einstellen: `Latin1`, `ISO-8859-1`, nicht `UTF-8` - woher weiß ich das?
        
        - weil es bei den meisten GIS-Daten der Länder-GIS Abteilungen (leider) so ist?
        
        - weil es in `mountainbike_tafeln.cst` steht

        - weil es nach Shapefile-Spezifikation (<https://en.wikipedia.org/wiki/Shapefile>) eigentlich in `mountainbike_tafeln.cpq` stehen müsste (siehe auch <https://github.com/qgis/QGIS/issues/53811>)

        - `mountainbike_tafeln.cst` in `mountainbike_tafeln.cpg` umbenennen ist auch eine Lösung, dann geht's auch mit der automatischen Erkennung

- Abspeichern als `.geojson`

    - Rechte Maus auf "*Layer / Export / Objekte speichern als*"

        - GeoJSON

        - Dateiname: `mountainbike.geojson`

        - KBS: EPSG:4326

        - optional *Select fields* anpassen

        - in jedem Fall *Persist layer metadata* checken, sonst gehen die properties verloren

        - COORDINATE_PRECISION: 5

        - RFC7946: YES  - was ist das? Das ist ein Verweise auf die GeoJSON Spezifikation <https://datatracker.ietf.org/doc/html/rfc7946>, kann also nicht schaden, wenn wir Standard konformes GeoJSON schreiben

        - WRITE_BBOX: YES - dann sehen wir die Koordinaten des Ausschnitts und können eventuell unseren Kartenausschnitt entsprechend setzen.

    - OK drücken und fertig

    COMMIT: <https://github.com/webmapping/tips/commit/b08bed64d2eae927e7cba4b8b6967e57d35c350b>

- visualisieren

    - Template <https://webmapping.github.io/templates/template-austria.html> verwenden und als `mountainbike.html` speichern

        COMMIT <https://github.com/webmapping/tips/commit/1e7c402e6496ab0e17c7c681b6485f1f93fe7d90>

    - Titel und Quelle des Templates anpassen

        COMMIT <https://github.com/webmapping/tips/commit/ee0204f5a14c96f704fd0d63ec0977eeba6b8044>
    
    - `mountainbike.geojson` landen und anzeigen

        ```javascript
        // GeoJSON asynchron laden
        async function loadGeoJSON(url) {
            let response = await fetch(url);
            let geojson = await response.json();

            L.geoJSON(geojson).addTo(map);
        }
        loadGeoJSON("mountainbike.geojson");
        ```

        COMMIT <https://github.com/webmapping/tips/commit/3448cd98715cea76765ed8c8e38fc946983c647e>

    - mit `.fitBounds()` <https://leafletjs.com/reference.html#map-fitbounds> und `.getBounds()` <https://leafletjs.com/reference.html#geojson-getbounds> können wir auf den Ausschnitt der GeoJSON-Daten blicken. Damit wir den Extent der Marker bekommen, müssen wir den `L.geoJSON()` Aufruf nur in einer Variablen `mtbLayer` speichern und dann dessen Ausdehnung mit `mtbLayer.getBounds()` abfragen. Gesetzt wird der Ausschnitt für die gesamte Karte, also beim Karten-Objekt mit `map.fitBounds()`.

        ```javascript
        // GeoJSON asynchron laden
        async function loadGeoJSON(url) {
            let response = await fetch(url);
            let geojson = await response.json();

            let mtbLayer = L.geoJSON(geojson).addTo(map);
            map.fitBounds(mtbLayer.getBounds());
        }
        loadGeoJSON("mountainbike.geojson");
        ```

        COMMIT <https://github.com/webmapping/tips/commit/fe153de7c917f7abc496dafc4f2c121303fd8838>

    - **Tipp**: mit `.fitBounds()` kann der Ausschnitt auch über die Bounding Box von `mountainbike.geojson` gesetzt werden, die wir beim Export zusätzlich mit der Option `WRITE_BBOX: YES` hinzugefügt haben. Aus dem Array mit `minXY`, `maxXY` wird ein Array mit zwei Arrays für `minLatLng` und `maxLatLng` - Achtung: die Koordinaten müssen natürlich wieder getauscht werden ...

        ```javascript
        // aus bbox: [ 9.60326, 46.86816, 10.18094, 47.53811 ] wird ...
        map.fitBounds([
            [46.86816, 9.60326],
            [47.53811, 10.18094],
        ]);

    - die fertige Seite online: <https://webmapping.github.io/tips/mountainbike.html>

## 3. GeoJSON mit Python in TopoJSON konvertieren

Wenn GeoJSON-Dateien zu groß für die Verwendung im Projekt sind, kann man versuchen, sie ihn **TopoJSON** umzuwandeln. Die Spezifikation dieses Formats findet ihr hier: <https://github.com/topojson/topojson-specification>

Ein Beispiel:

- wir suchen bei Google nach "*landkreise deutschland geojson*"

- und werden beim `OpenDataLab` fündig - <https://opendatalab.de/projects/geojson-utilities/>

    - wir setzen bei Optionen *"Landkreise ...*" und "*simplify 200*"
    
    - wählen alle aus
    
    - und speichern die Landkreise temporär als `landkreise_simplify200.geojson` (3,2MB)

- mit einem provisorischen GeoJSON nach TopoJSON Konvertierungstool am Server der Geographie, können wir sie umwandeln: <https://geographie.uibk.ac.at/data/topojson>

    - das dauert :-)

    - und wird nach der Projektphase wieder offline genommen

    - wer "Python 3 fit" ist kann sich dieses Tool auch selbst bauen:

        - das **topojson Modul** installieren - <https://pypi.org/project/topojson/>

        - dazu das **geojson Modul** installieren  - <https://pypi.org/project/geojson/>

        - dieses Skript konvertiert dann die GeoJSON-Daten in `landkreise_simplify200.geojson` nach TopoJSON als `landkreise.topojson`

            ```python
            import topojson
            import json

            # convert GeoJSON to TopoJSON
            with open("landkreise_simplify200.geojson", "r", encoding="UTF-8") as fh_in:
                data = json.load(fh_in)
                assert data["type"] == "FeatureCollection"
                topology = topojson.Topology(data)
                with open("landkreise.topojson", "w", encoding="UTF-8") as fh_out:
                    fh_out.write(topology.toposimplify(0.0001).to_json())
                    print("created ", fh_out.name)
            ```

        - das Resultat `landkreise.topojson` hat nur noch `~800KB`. Wenn man vor der Konvertierung in QGIS alle Attribut-Spalten bis auf `AGS` (Kreisschlüssel) und `GEN` (Kreisname) löscht, kommt man immerhin auf `~650KB`.

            COMMIT <https://github.com/webmapping/tips/commit/1a330f6f8404484b1672cbfc549c2354dca36b8c>

## 4. TopoJSON mit Leaflet.omnivore visualisieren

- als Template verwenden wir <https://webmapping.github.io/templates/template-europe.html> und speichern es als `landkreise.html`

    COMMIT <https://github.com/webmapping/tips/commit/6ab65c87b6513b1d6ddf6734efc2bbaad16e74ef>

- Titel und Quelle des Templates anpassen

    COMMIT <https://github.com/webmapping/tips/commit/7e81b9c4a5b073e1f41fa2f5dcf200ad38d2ccd7>

- das **Leaflet.omnivore Plugin** <https://github.com/mapbox/leaflet-omnivore> in `index.html` referenzieren

    ```html
    <!-- Leaflet Omnivore -->
    <script src='//api.tiles.mapbox.com/mapbox.js/plugins/leaflet-omnivore/v0.3.1/leaflet-omnivore.min.js'></script>
    ```

    COMMIT <https://github.com/webmapping/tips/commit/e613639612054ab093c7aefe6c910e7e933c6fd6>

- die TopoJSON-Daten laden und anzeigen

    ```javascript
    omnivore.topojson("landkreise.topojson").addTo(map);
    ```

    COMMIT <https://github.com/webmapping/tips/commit/00e0cc7af5d1213cce59af15dbf66dcfe22d19d9>

- damit wir mit den Daten, die Leaflet.omnivore geladen hat gleich wie mit GeoJSON arbeiten können (also `filter`, `onEachFeature`, etc. verwenden können) müssen wir noch eine Trick anwenden: wir definieren eine sogenannte "*Callback-Funktion*" als `L.geoJSON()` Aufruf - wir nennen sie `drawLandkreise` und referenzieren sie beim Laden der Daten mit Leaflet.omnivore. Das Plugin kümmert sich dann um den Rest und wir können in der "*Callback-Funktion*" wie von GeoJSON her gewohnt arbeiten. Der folgende Code definiert dieses Callback und implementiert ein Popup mit dem Namen der Landkreise:

    ```javascript
    let drawLandkreise = L.geoJson(null, {
        onEachFeature: function(feature, layer) {
            //console.log(feature)
            layer.bindPopup(`
                <h4>${feature.properties.GEN}</h4>
            ` )
        }
    });
    omnivore.topojson("landkreise.topojson", null, drawLandkreise).addTo(map);
    ```

    COMMIT <https://github.com/webmapping/tips/commit/54667b8aa3ab59fc8c6049e6493bc2fc7a1b8b6b>

- bleibt noch, auf den Ausschnitt zu blicken - wir versuchen es wieder mit `.fitBounds()`  und `.getBounds()`

    ```javascript
    let topoLayer = omnivore.topojson("landkreise.topojson", null, drawLandkreise).addTo(map);
    map.fitBounds(topoLayer.getBounds);
    ```

- **HMMM** - scheinbar funktioniert das Ausschnitt setzen nicht, den wir bekommen in der Konsole den Fehler `Uncaught Error: Bounds are not valid`. Scheinbar sind die Landkreise zum Zeitpunkt unseres `.fitBounds()` Aufrufs noch nicht geladen - wir müssen also warten, bis alles gezeichnet ist und das können wir mit einem **Event-Listener* implementieren:

    ```javascript
    let topoLayer = omnivore.topojson("landkreise.topojson", null, drawLandkreise).addTo(map);
    topoLayer.on("ready", function() {
        map.fitBounds(topoLayer.getBounds())
    });
    ```

    COMMIT <https://github.com/webmapping/tips/commit/bc1c8eb27eb77d4a3bb400b2a0942a8bf9ca9e9a>

- die fertige Seite online: <https://webmapping.github.io/tips/landkreise.html>

## 5. zwei Plugins zum Schluss

- Template <https://webmapping.github.io/templates/template-tirol.html> verwenden und als `plugins_hash_locate.html` speichern

    COMMIT <https://github.com/webmapping/tips/commit/64bc6d802b97f89f007b9260a4e88510e31ae1f9>

- Titel und Quelle des Templates anpassen

    COMMIT <https://github.com/webmapping/tips/commit/5f3bd7c7e421b6e8b857f8b928c7652d1ee73209>

- **Leaflet.hash** - <https://github.com/mlevans/leaflet-hash>

    - es zeigt Zoom-Level, Lat, Lng in der URL-Zeile des Browsers an. Wir können damit gut Bookmarks zu Ausschnitten erzeugen

    - Script in `plugins_hash_locate.html` über `cdnjs.com` <https://cdnjs.com/libraries/leaflet-hash> referenzieren

        ```html
        <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-hash/0.2.1/leaflet-hash.js" integrity="sha512-2KBa5eJPxTH3HX7jCWrbO+NYNLV8xrnX5lDB7FTs9HFJ8jpO1MTV6MkmNNaia79k+xOk8q965FkqAjwEYCfDAA==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
        ```
    
    - initialisieren

        ```javascript
        new L.Hash(map);
        ```

    COMMIT <https://github.com/webmapping/tips/commit/e9ed868ff4eb055cd8a4561e7d78f99db59d0042>

- **Leaflet.locatecontrol** - <https://github.com/domoritz/leaflet-locatecontrol>

    - es ermöglicht, auf den eigenen Standort zu zoomen. Praktisch bei Karten, bei denen wir GeoLocation zur Navigation beim Benutzer, der Benutzerin benötigen

    - Stylesheet und Script in `plugins_hash_locate.html` über `cdnjs.com` <https://cdnjs.com/libraries/leaflet-locatecontrol> referenzieren

        ```html
        <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet-locatecontrol/0.81.1/L.Control.Locate.css" integrity="sha512-6+fQwheLeCW6sKV5liSHZ0rxcrN4TRBwWOhsof+3Iahu9J+WRyF4eBLjL5QshWxja6WiKE1WAtrH0hsRJ3nA8A==" crossorigin="anonymous" referrerpolicy="no-referrer" />
        <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-locatecontrol/0.81.1/L.Control.Locate.min.js" integrity="sha512-s/EGgrVhzEldZLssatR0N5oT6POS4r2ZwYa+sCXYrx8YMlKn9TQCIGTJS4c4O7w/u+bw32sNEyo+EPa+KDrw3Q==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>

        ```

    - initialisieren

        ```javascript
        L.control.locate({
            strings: {
                title: "Eigenen Standort anzeigen"
            }
        }).addTo(map);
        ```

    COMMIT <https://github.com/webmapping/tips/commit/d101d968c65e848d989fdf3c09d0b4e611f91b37>

- die fertige Seite online: <https://webmapping.github.io/tips/plugins_hash_locate.html>
