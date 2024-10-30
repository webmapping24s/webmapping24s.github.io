# Session 6: Styling von Flächen, Linien, Punkten & Leaflet.markercluster

## Fläche der Fußgängerzonen

- bevor wir die Flächen stylen, können wir auch noch das Popup der Fußgängerzonen verfeinern, denn es gibt Fußgängerzonen, bei denen die Attribute `feature.properties.ZEITRAUM`und / oder `feature.properties.AUSN_TEXT` leer sind. Diese Fälle können wir mit dem sogenannten logical OR operator "\|\|" (<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR>) abfangen und statt "*null*", wie es derzeit passiert, etwas Sinnvolles hinschreiben. Der veränderte **Template string** der beiden Attribute sieht dann so aus:

    ```javascript
    ${feature.properties.ZEITRAUM || "dauerhaft"}           // ersetzt leere Einträge mit "dauerhaft"
    ${feature.properties.AUSN_TEXT || "ohne Ausnahme"}      // ersetzt leere Einträge mit "ohne Ausnahme"
    ```

    COMMIT: <https://github.com/webmapping24s/wien/commit/e6e60d4bd1bd49ab888ba2d533ac6053e8c17a32>

- das Aussehen von Flächen ist sehr einfach über eine GeoJSON `style` Option zu ändern - siehe <https://leafletjs.com/reference.html#geojson-style>

    ```javascript
    function (feature) {
        return {}
    }
    ```

- `return` liefert ein `Path options` Objekt zurück und bestimmt, wie die Fläche/Linie aussehen soll - siehe <https://leafletjs.com/reference.html#path-option>

- wir ändern:

    - die Standardfarbe ([color](https://leafletjs.com/reference.html#path-color)) auf <https://clrs.cc/> `FUCHSIA (#F012BE)`

    - die Strichstärke  ([weight](https://leafletjs.com/reference.html#path-weight)) der Linie auf `1`

    - die Transparenz der Randlinie ([opacity](https://leafletjs.com/reference.html#path-opacity)) auf `0.4`
    
    - die Transparenz der Fläche ([fillOpacity](https://leafletjs.com/reference.html#path-fillopacity)) auf `0.1`


    ```javascript
    L.geoJSON(geojson, {
        style: function(feature) {
            return {
                color: "#F012BE",
                weight: 1,
                opacity: 0.4,
                fillOpacity: 0.1,
            }
        }
    })
    ```

    COMMIT: <https://github.com/webmapping24s/wien/commit/18c54e96259661cc33255b4ab2b12ec6ddd28d22>


## Linien der Buslinien

- auch die Linien sind, wie bei den Flächen, sehr einfach über eine GeoJSON `style` Option zu ändern - siehe <https://leafletjs.com/reference.html#geojson-style>

- wir ändern zuerst einmal alle Linien auf die Farbe Grün

    ```javascript
    L.geoJSON(geojson, {
        style: function (feature) {
            return {
                color: "green"
            };
        }
    });
    ```

- nachdem unsere Buslinien Namen nach Farben haben, liegt es nahe, sie auch in der passenden Farbe zu zeichnen

- wo bekommen wir Farben her? Wieder hier: <https://clrs.cc/> und wir werden `RED`, `YELLOW`, `Blue`, `GREEN`, `GRAY` und `ORANGE` brauchen

- wie können wir die passende Farbe zuordnen? In `feature.properties.LINE_NAME` steht für jedes Liniensegment der Linienname: "Red Line", "Yellow Line", "Blue Line", "Green Line", "Grey Line" oder "Orange Line"

- wir müssen also nur in einer `if-Abfrage` die richtige Farbe ermitteln, bevor wir sie mit `return` zurückgeben. hier ist der Code, der das macht:

    ```javascript
    L.geoJSON(geojson, {
        style: function (feature) {
            let lineName = feature.properties.LINE_NAME;
            let lineColor = "black";

            if (lineName == "Red Line") {
                lineColor = "#FF4136";
            } else if (lineName == "Yellow Line") {
                lineColor = "#FFDC00";
            } else if (lineName == "Blue Line") {
                lineColor = "#0074D9";
            } else if (lineName == "Green Line") {
                lineColor = "#2ECC40";
            } else if (lineName == "Grey Line") {
                lineColor = "#AAAAAA";
            } else if (lineName == "Orange Line") {
                lineColor = "#FF851B";
            } else {
                // vielleicht kommen noch andere Linien dazu ...
            }

            return {
                color: lineColor
            };
        }
    });
    ```

    COMMIT: <https://github.com/webmapping24s/wien/commit/cbcad01575d27d9b02f1abd31beb79f3b9ba0025>

    - es gäbe elegantere Wege zur Ermittlung der Farbwerte als die `if-Abfragen`, aber für's Erste reicht uns das ...

## Icons für die Sehenswürdigkeiten, Bushaltestellen und Hotels

Bis jetzt haben wir für Punkte nur Marker verwendet - das soll sich jetzt ändern, denn wir werden Icons für die Sehenswürdigkeiten und Bushaltestellen in passenden Farben verwenden

### Task 1: Icons finden

- wo bekommen wir Icons her? Die **Map Icons Collection** unter <https://mapicons.mapsmarker.com/> bietet eine Vielzahl an Icons

- Sehenswürdigkeiten:

    - wir suchen nach *photo* und wählen das Photo-Icon

    - wir versuchen gleich das *customizable* Feature und wählen Weiß (`#ffffff`) als Farbe beim Farbwähler und klicken auf `Generate`

    - die Seite wird neu geladen, die Icons entsprechend angepasst und wir finden unsere Farbe in der URL <https://mapicons.mapsmarker.com/markers/media/photo/?custom_color=ffffff> wieder - dieses Feature werden wir später bei den Icons für die Bushaltestellen noch brauchen ...

    - wir wählen das linke Icon und speichern es mit Rechtsklick im `icons/`-Verzeichnis als `photo.png`

    COMMIT: <https://github.com/webmapping24s/wien/commit/6b8e9fa1b7144582d148e9555826910837f2c63d>

- Bushaltestellen:

    - wir suchen nach *bus* und wählen das Bus-Icon

    - nachdem wir sechs Sightseeing Linien haben, brauchen wir auch sechs Busicons für die Haltestellen in den passenden Farben. Eine gute Verbindung zwischen Haltestellenfarbe und Icon-Name können wir über `feature.properties.LINIE_ID` herstellen, denn dort stehen Zahlen für die jeweilige Linienfarbe (z.B. 1 für "Red Line", 2 für "Yellow Line", usw.). Deshalb erzeugen wir sechs Icons mit Farben von <https://clrs.cc/> und speichern sie als `bus_1.png` bis `bus_6.png`.


        - `bus_1.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=FF4136> (RED)
        - `bus_2.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=FFDC00> (YELLOW)
        - `bus_3.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=0074D9> (BLUE)
        - `bus_4.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=2ECC40> (GREEN)
        - `bus_5.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=AAAAAA> (GRAY)
        - `bus_6.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=FF851B> (ORANGE)

    - wir wählen wieder immer das linke Icon und speichern es mit Rechtsklick im `icons/`-Verzeichnis als `bus_1.png` bis `bus_6.png`

    COMMIT: <https://github.com/webmapping24s/wien/commit/de9ea0c5124d03478f4d4262ae60d62f506957c8>

- Hotels:

    - für Hotels und Unterkünfte suchen wir sechs Icons, die sich nach der Anzahl der Sterne der Unterkunft in `feature.properties.KATEGORIE_TXT` richten werden. Farblich sollen alle gleich in <https://clrs.cc/> `PURPLE (#B10DC9)` sein

    - wir suchen *hotel*, wählen die Hotel-Icons `hotel_0star` bis `hotel_5stars`, stellen die Farbe ein und speichern die Icons als `hotel_0star.png` bis `hotel_5stars.png`

    - der Link zu den generierten Icons ist <https://mapicons.mapsmarker.com/markers/restaurants-bars/hotels/hotel/?custom_color=b00dc9>

    COMMIT: <https://github.com/webmapping24s/wien/commit/9fd70b4c8ea0be3a6ea9900b25bfe10b360d31ec>

Damit haben wir alle Icons vorbereitet und wir können sie bei den jeweiligen Layern einbauen

COMMIT: 

### Task 2: Marker der Sehenswürdigkeiten durch Kamera-Icons ersetzen

- Marker können wir sehr einfach über eine GeoJSON `pointToLayer` Option mit Icons ersetzen - siehe <https://leafletjs.com/reference.html#geojson-pointtolayer>

    ```javascript
    L.geoJSON(geojson, {
        pointToLayer: function(feature, latlng) {
            return L.marker(latlng);
        }
    });
    ```

- `pointToLayer` erwartet eine Funktion, der der jeweilige Punkt und seine Koordinate übergeben wird. Mit `return` wird im einfachsten Fall ein Standardmarker zurückgegeben, den wir aber durch ein `L.Icon` ersetzen können. Mit `iconUrl` können wir bestimmen, welches Bild als Icon verwendet werden soll - siehe <https://leafletjs.com/reference.html#icon>

    ```javascript
    L.geoJSON(geojson, {
        pointToLayer: function(feature, latlng) {
            return L.marker(latlng, {
                icon: L.icon({
                    iconUrl: "icons/photo.png",
                })
            })
        }
    });
    ```

- so weit so gut, leider müssen wir die Koordinate des Icons und den Popup Anker noch anpassen, denn per Default passt beides weder zur echten geographischen Lage, noch zu jenem Punkt, an dem sich das Popup öffnet. Anfasspunkt von Icons ist nämlich Links/Oben.

    - die Optionen `iconAnchor`, `popupAnchor` des `L.icon` Objekts werden diese Anfasspunkte jeweils als Arrays mit X und Y definieren

    - die Spitze unseres Icons mit der Größe **32x37** Pixel liegt von Links, Oben gesehen bei **16** und **37** und diese Werte verwenden wir beim `iconAnchor` Array

    - wenn wir auf das Popup klicken, verdeckt das Popup das Icon, deshalb schieben wir es beim `popupAnchor` Array um 37 Pixel nach Oben

    ```javascript
        L.geoJSON(geojson, {
            pointToLayer: function(feature, latlng) {
                return L.marker(latlng, {
                    icon: L.icon({
                        iconUrl: "icons/photo.png",
                        iconAnchor: [16, 37],
                        popupAnchor: [0, -37]
                    })
                })
            }
        });
    ```

    COMMIT: <https://github.com/webmapping24s/wien/commit/b5c8bc5a8deffa38aeac0fc78128579a65265733>

Gut, dass alle Icons die selbe Größe haben, denn wir können damit für alle die selben Anfasspunkte verwenden. Das Einzige, was sich ändern wird, sind die Links zu den Icons im `icon/` Verzeichnis

### Task 3: Marker der Haltestellen durch Bus-Icons nach Linienfarbe ersetzen

- wir erinnern uns, dass die ID und damit die Farbe der Linie im Attribut `feature.properties.LINIE_ID` verfügbar ist und wir bereits Bus-Icons in den richtigen Farben von `bus_1.png` bis `bus_6.png` erstellt haben.

- wir erinnern uns auch, dass wir Icons über das GeoJSON Options-Objekt `pointToLayer` erzeugen und die Anfasspunkte über `iconAnchor` und `popupAnchor` festlegen können

- der Code für Bus-Icons in Farben der Buslinien ist damit nur eine kleine Modifikation des Foto-Icons der Sehenswürdigkeiten 

- wir ersetzen über einen **Template string** die Zahl beim Bus-Icon durch den `feature.properties.LINE_ID` der jeweiligen Haltestelle

    ```javascript
        L.geoJSON(geojson, {
            pointToLayer: function(feature, latlng) {
                return L.marker(latlng, {
                    icon: L.icon({
                        iconUrl: `icons/bus_${feature.properties.LINE_ID}.png`,
                        iconAnchor: [16, 37],
                        popupAnchor: [0, -37]
                    })
                })
            }
        });
    ```

    COMMIT <https://github.com/webmapping24s/wien/commit/a1975eb96877c225a0246f6dda04439c92027551>

### Task 4: Marker der Hotels durch unterschiedliche Icons nach Sternen der Unterkunft ersetzen

- wie bei den Farben der Linien, verwenden wir eine **if-Abfrage** zur Bestimmung des Namens des jeweiligen Hotelicons

    ```javascript
    L.geoJSON(geojson, {
        pointToLayer: function(feature, latlng) {
            let hotelKat = feature.properties.KATEGORIE_TXT;
            let iconName;

            if (hotelKat == "nicht kategorisiert") {
                iconName = "hotel_0star";
            } else if (hotelKat == "1*") {
                iconName = "hotel_1star";
            } else if (hotelKat == "2*") {
                iconName = "hotel_2stars";
            } else if (hotelKat == "3*") {
                iconName = "hotel_3stars";
            } else if (hotelKat == "4*") {
                iconName = "hotel_4stars";
            } else if (hotelKat == "5*") {
                iconName = "hotel_5stars";
            } else {
                iconName = "hotel_0star";
            }

            return L.marker(latlng, {
                icon: L.icon({
                    iconUrl: `icons/${iconName}.png`,
                    iconAnchor: [16, 37],
                    popupAnchor: [0, -37]
                })
            })
        },
    ```

    COMMIT: <https://github.com/webmapping24s/wien/commit/e904affee44026600b80045c70521d9420585bd5>

### Task 5: Icons der Hotels mit Leaflet.markercluster automatisch clustern

- dafür gibt es ein Plugin bei Leaflet unter "*Clustering/decluttering*" <https://leafletjs.com/plugins.html#clusteringdecluttering>

- **Leaflet.markercluster** heißt es und ist hier zu finden <https://github.com/Leaflet/Leaflet.markercluster>

- wir verwenden für *Include the plugin CSS and JS files* wieder *cdnjs* - <https://cdnjs.com/libraries/leaflet.markercluster>

- in `index.html` verlinken wir `leaflet.markercluster.js`, `MarkerCluster.Default.css` und `MarkerCluster.css`

    ```html
    <!-- Marker cluster -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.5.3/leaflet.markercluster.js" integrity="sha512-OFs3W4DIZ5ZkrDhBFtsCP6JXtMEDGmhl0QPlmWYBJay40TT1n3gt2Xuw8Pf/iezgW9CdabjkNChRqozl/YADmg==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.5.3/MarkerCluster.Default.css" integrity="sha512-6ZCLMiYwTeli2rVh3XAPxy3YoR5fVxGdH/pz+KMCzRY2M65Emgkw00Yqmhh8qLGeYQ3LbVZGdmOX9KUjSKr0TA==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.5.3/MarkerCluster.css" integrity="sha512-mQ77VzAakzdpWdgfL/lM1ksNy89uFgibRQANsNneSTMD/bj0Y/8+94XMwYhnbzx8eki2hrbPpDm0vD0CiT2lcg==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    ```

- in `main.js` verwenden wir eine `markerClusterGroup` bei der Definition des Layers für die Hotels und das war's dann auch schon :-)

    ```javascript
    let themaLayer = {
        // alle anderen Layer
        hotels: L.markerClusterGroup().addTo(map),
    }
    ```

- wir verfeinern das Verhalten beim Clustern und legen über eine Option fest, dass ab Zoom-Level 17 alle Icons ohne Cluster gezeigt werden sollen

    ```javascript
    let themaLayer = {
        // alle anderen Layer
        hotels: L.markerClusterGroup({
            disableClusteringAtZoom: 17
        }).addTo(map),
    }
    ```

COMMIT: <https://github.com/webmapping24s/wien/commit/d17085f020592d252dccec5c5799916cbe959489>
