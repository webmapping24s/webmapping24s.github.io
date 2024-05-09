# Session 7: AWS Euregio

## AWS Beispiel vorbereiten

- Template auspacken - neu ist eigentlich nur der WMTS-Layer von <https://lawinen.report> in `L.control.layers()`

    COMMIT <https://github.com/webmapping/aws/commit/09a54457f4472ed2cc4142a6ea6d0bb32a2ee107>

## Wetterstationen als Icon mit Popup visualisieren

### 1. Icons für die Wetterstationen

- Wetterstationen als Standardmarker in der async-Function hinzufügen

    ```javascript
    //console.log(geojson)
    L.geoJSON(geojson).addTo(themaLayer.stations);
    ```

    COMMIT <https://github.com/webmapping/aws/commit/6530c8628c83db704002c4aad0821446bc87e6cd>

- Stations-Icon hinzufügen

    COMMIT <https://github.com/webmapping/aws/commit/96095e39ca2ae561b0d503e33b77bc31ae90e62b>

- Stations-Icon mit `pointToLayer` Option implementieren und positionieren

    ```javascript
    L.geoJSON(geojson, {
        pointToLayer: function(feature, latlng) {
            return L.marker(latlng, {
                icon: L.icon({
                    iconUrl: "icons/wifi.png",
                    iconAnchor: [16, 37],
                    popupAnchor: [0, -37]
                })
            });
        },
        // Popup ...
    }).addTo(themaLayer.stations);
    ```

    COMMIT <https://github.com/webmapping/aws/commit/1b20153ab5603c541b40a28221eaee403028c7a0>

### 2. Popups für die Wetterstationen

- Popup mit `onEachFeature` Option hinzufügen - wie beim Wien-Beispiel mit logical OR || Operator

    ```javascript
    L.geoJSON(geojson, {
        onEachFeature: function(feature, layer) {
            layer.bindPopup(`
                <h4>${feature.properties.name} (${feature.geometry.coordinates[2]}m)</h4>
                <ul>
                    <li>Lufttemperatur (°C): ${feature.properties.LT || "-"}</li>
                    <li>Relative Luftfeuchte (%): ${feature.properties.RH || "-"}</li>
                    <li>Windgeschwindigkeit (km/h): ${feature.properties.WG ||"-"}</li>
                    <li>Schneehöhe (cm): ${feature.properties.HS || "-"}</li>
                </ul>
                <span>${feature.properties.date}</span>
            `);
        }
    }).addTo(themaLayer.stations);
    ```

    COMMIT <https://github.com/webmapping/aws/commit/40240f713d660b904ddafdae4b819335e682cf8c>

- Abfrage bei möglicherweise fehlenden Werten im Popup verfeinern - wir verwenden den **Conditional (ternary) operator** (<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_operator>) mit **Bedingung / ? Fragezeichen / : Doppelpunkt**

    ```javascript
    layer.bindPopup(`
        <li>Lufttemperatur (°C): ${feature.properties.LT != undefined ? feature.properties.LT : "-"}</li>
    `);
    ```

    - ist so zu lesen: wenn die Bedingung "Lufttemperatur ist nicht `undefined`" zutrifft, dann schreibe die Lufttemperatur ins Popup, sonst ein Minuszeichen

    COMMIT <https://github.com/webmapping/aws/commit/9b9d096c115aa4e6640226bd093841bf42813bbb>

- mehr Layoutkontrolle bei den Werten im Popup - wir verwenden `.toFixed(1)`, etc.

    COMMIT <https://github.com/webmapping/aws/commit/e70c919de6e399fb1284404fac02500f502fb827>

- das Datum für den Messwert können wir auch noch lesbarer formatieren

    - Datumsberechnungen in jeder Programmiersprache schwierig, weil so viele Formate und Zeitzonen.

    - Im Datensatz ist die Angabe der Zeit günstig, weil in einem standardisierten Format:

        ```text
        2023-05-11T12:20:00+02:00
        ```

    - wird von der Javascript `new Date()` Funktion erkannt und kann als *echtes* Datum verwendet werden.

    - Formatiert nach der aktuelle eingestellten Sprache im Browser mit `toLocaleString()`

        ```javascript
            let pointInTime = new Date(feature.properties.date);
            layer.bindPopup(`
                // markup ...
                <span>${pointInTime.toLocaleString()}</span>
            `);
        ```

        COMMIT <https://github.com/webmapping/aws/commit/a9fc2e0b4a46eb6355bfa74a13111c26e36e616e>

## Temperaturwerte als Text mit Hintergrundfarbe nach einer Skala darstellen

### 1. einen neuen Temperatur-Layer erstellen

```javascript
let themaLayer = {
    stations: L.featureGroup(),
    temperature: L.featureGroup()
}
```

### 2. eine neue Funktion zum Zeichnen der Temperatur definieren

- diese Funktion verwendet die bereits geladenen GeoJSON-Daten und wird aus der Funktion, in der die Daten geladen werden aufgerufen

```javascript
function showTemperature(geojson) {
    // DIV-Icons mit den GeoJSON-Daten zeichnen
}

async function showStations(url) {
    let response = await fetch(url);
    let geojson = await response.json();

    // Stations-Marker zeichnen

    // Temperatur-Layer zeichnen
    showTemperature(geojson);
```

### 3. L.divIcon() für Marker mit Text verwenden

- den Code der Option `icon` ersetzen wir mit einem [L.divIcon()](https://leafletjs.com/reference.html#divicon), dessen Optionen-Objekt aus zwei Key/Value Paaren besteht:

    - die Option [className](https://leafletjs.com/reference.html#divicon-classname) gibt jedem &lt;div> Element das wir als Icon erstellen ein Klassenattribut `class="aws-div-icon"` - wir können es dann zum Stylen der Texte verwenden

    - die Option [html](https://leafletjs.com/reference.html#divicon-html) bestimmt den HTML-Markup, der angezeigt werden soll. Wir setzen ihn als &lt;span> Element mit Template Syntax und Backticks auf die Lufttemperatur im Attribut `feature.properties.LT`

    ```javascript
    icon: L.divIcon({
        className: "aws-div-icon",
        html: `<span>${feature.properties.LT}</span>`
    })
    ```

- die gesamte Funktion `showTemperature()` sieht damit so aus

    ```javascript
    function showTemperature(geojson) {
        L.geoJSON(geojson, {
            pointToLayer: function(feature, latlng) {
                return L.marker(latlng, {
                    icon: L.divIcon({
                        className: "aws-div-icon",
                        html: `<span>${feature.properties.LT}</span>`
                    })
                })
            }
        }).addTo(themaLayer.temperature);
    }
    ```

    COMMIT <https://github.com/webmapping/aws/commit/57da158668407ff3a279d9568a2050ec6348cf26>

### 4. den Text Marker in main.css stylen

- über die Klasse `.aws-div-icon` können wir die Texte des &lt;span> Elements im Stylesheet `main.css` stylen:

    ```css
    .aws-div-icon span {
        border: 1px solid silver;
        font-size: 1.25em;
        font-weight: bold;
        padding: 0.3em;
        text-shadow:
            -1px -1px 0 white,
            -1px 1px 0 white,
            1px -1px 0 white,
            1px 1px 0 white;
        display: inline-block;
        transform: translate(-30%, -50%);
    } 
    ```

    COMMIT <https://github.com/webmapping/aws/commit/8c30d9cfc370fd76c9a4cdab24761edbdec13388>
    COMMIT <https://github.com/webmapping/aws/commit/f0e47a239f11257e5081d55155eb58c046671df7>

- die ersten vier CSS Regeln sind selbsterklärend

- die `text-shadow` Anweisung ist für uns in dieser Form neu. Sie sorgt dafür, dass auf allen Seiten des Textes ein weißer Schatten entsteht, der den Text auch auf farbigem Hintergrund (wie wir ihn später implementieren werden) gut lesbar macht. Mit `display` und `transform` verschieben wir den Mittelpunkt der Icons ungefähr dahin, wo die Koordinate des Markers ist. Ganz genau ist das nicht, aber für unsere Zwecke reicht es.

- leider ist auch die Position des Icons noch nicht ganz richtig :-(

    - ... und wird es auch nicht hundertprozentig werden, denn die Positionierung von DIV-Icons ist nicht gerade einfach. Fügen wir vor dem `return` des DIV-Icons mit `L.marker(latlng).addTo(map)` einen zweiten Marker an der selben Position ein, erkennen wir, dass die Koordinate "ungefähr" links, oben beim Text liegt. Über einen Workaround im CSS von `main.css` verschieben wir deshalb die Icons nach links oben:

        ```css
        .aws-div-icon span {
            /* bestehende CSS Regeln */
            display: inline-block;
            transform: translate(-30%, -50%);
        }
        ```

    - das Verschieben der Marker bewirkt die CSS-Eigenschaft [transform](https://developer.mozilla.org/en-US/docs/Web/CSS/transform), deren `translate` Anweisung zwei Offsets in `x` und `y` Richtung erwartet. Warum wir beim Verschieben `-30%` für die x-Richtung verwenden müssen bleibt allerdings ein Rätsel. Immerhin liegt der Textmarker jetzt mit seinem Zentrum an der annähernd richtigen Position.

    - **Hinweis**: mit [transform](https://developer.mozilla.org/en-US/docs/Web/CSS/transform) und der `rotate` Anweisung könnten wir die Marker auch drehen ...

### 5. GeoJSON Objekte beim Zeichnen filtern

- zwei Verbesserungen müssen wir in `main.js` noch implementieren. Vor allem die Stationen mit `undefined` bereiten uns Probleme, wir müssen sie daher beim Zeichnen ignorieren. Leaflet stellt uns dafür die GeoJSON Option [filter](https://leafletjs.com/reference.html#geojson-filter) zur Verfügung

- genau wie `pointToLayer` ist [filter](https://leafletjs.com/reference.html#geojson-filter) eine Funktion, die für jedes Element in GeoJSON ausgeführt wird. Liefert sie `true` zurück, wird der Marker gezeichnet. In der Funktion können wir auf die Attribute unseres Markers zugreifen und so entscheiden, ob wir `true` zurückgeben möchten. Wir schreiben die Option oberhalb von `pointToLayer` und liefern `true` zurück, wenn die Temperatur zwischen -50° und +50° liegt:

    ```javascript
    filter: function (feature) {
        if (feature.properties.LT > -50 && feature.properties.LT < 50) {
            return true;
        }
    }
    ```

- neu bei dieser if-Abfrage ist die Verknüpfung zweier Bedingungen mit dem [Logical AND (&&)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND) Operator

- damit verschwinden Stationen ohne Temperatur und wir können die zweite Verbesserung angehen - die Zahl der Nachkommastellen mit der Javascript Methode [.toFixed](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toFixed) auf eine Nachkommastelle festlegen:

    ```javascript
    html: `<span>${prop.LT.toFixed(1)}</span>`
    ```

COMMIT <https://github.com/webmapping/aws/commit/34383677b5e9b9c7e40796a5d24e09d26f30bbf5>

- dieser Schritt hätte ohne das Ausschließen von Stationen ohne Temperatur unser Skript gestoppt, denn der Versuch den Wert `undefined` auf eine Nachkommastelle zu formatieren wäre natürlich gescheitert.

## Temperaturwerte in Farben umsetzen

### 1. Farb-Objekt COLORS mit Schwellen und Farben

- zur Umsetzung der Temperaturwerte in Farben der Rechtecke, definieren wir zunächst in einer neuen Javascript-Datei `colors.js` ein `COLORS`-Objekt  mit Farbwerten und dazugehörigen min/max Schwellen. Visual Studio Code hilft uns beim Erstellen im &lt;head> Bereich von `index.html` mit dem Baustein `script:src` und `STRG-Klick` auf den generierten Link. Den Kommentar dort erweitern wir entsprechend.

    ```html
    <!-- eigene Stile, Farben und Hauptskript -->
    <link rel="stylesheet" href="main.css">
    <script src="colors.js"></script>
    <script defer src="main.js"></script>
    ```

- die Farben mit Schwellen "leihen" wir uns von der [Temperaturkarte von Lawinen.report](https://lawinen.report/weather/map/temp). Mit *Rechtsklick / Untersuchen* bei den farbigen Legenden-Kästchen können wir die CSS Farbwerte der einzelnen Kästchen ablesen. Wir notieren sie als `color` mit `min`/`max` Schwellen in unserem `COLORS`-Objekt das a) eine Konstante ist, b) aus Objekten je nach Layern und c) darin aus einem Array von Objekten für die Definition besteht. Und weil das auch bei der [Windkarte](https://lawinen.report/weather/map/temp) funktioniert, definieren wir auch gleich noch Regeln für die Windgeschwindigkeit - so sieht unser COLORS-Objekt dann verkürzt aus:

    ```javascript
    const COLORS = {
        temperature: [
            { min: -50, max: -25, color: "#9f80ff" },
            { min: -25, max: -20, color: "#784cff" },
            // und so weiter
        ],
        wind: [
            { min: 0, max: 5, color: "#ffff64" },
            { min: 5, max: 10, color: "#c8ff64" },
            // und so weiter
        ]
    };
    ```

COMMIT <https://github.com/webmapping/aws/commit/851450ba235ab8c9bc4bb20883bf1d12e08dae5c>

- das vorläufige Resultat ist in [colors.js](https://webmapping.github.io/aws/colors.js) sichtbar ...

### 2. Funktion getColor(value, ramp) definieren

- nachdem wir den Task **eine Farbe für einen Wert nach Schwellen ermitteln** noch öfters brauchen werden, definieren wir eine Funktion `getColor`, die das für uns übernehmen wird. Sie erwartet beim Aufruf den Wert und die Farbpalette mit Schwellen, die in der Funktion dann als `value` und `ramp` verfügbar sind. In einer [for of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) Schleife werden alle Einträge in `ramp` mit dem `value` verglichen und sobald der Wert in die Schwellen passt, die entsprechende Farbe zurückgegeben. Wir schreiben die Funktion  noch vor der Funktion für das Zeichnen der Stationen und testen sie mit einem Aufruf, dem wir den Wert -40 und die Farbpalette COLORS.temperature übergeben.

- wie erwartet, bekommen wir den Farbwert `#9f80ff` angezeigt. Er steht für Temperaturen zwischen -50°  und -25°.

    ```javascript
    // Farben nach Schwellen ermitteln
    function getColor(value, ramp) {
        for (let rule of ramp) {
            if (value >= rule.min && value < rule.max) {
                return rule.color;
            }
        }
    }
    console.log(getColor(-40, COLORS.temperature));
    ```

COMMIT <https://github.com/webmapping/aws/commit/010e3d0c3f2119c86d36d69fe4c6d4c690b689f3>

### 3. Die ermittelte Farbe als style-Attribut beim &lt;span> Element setzen

- jetzt müssen wir nur noch in `pointToLayer` die richtige Farbe für das `L.divIcon` ermitteln, in `let color` speichern und als `style-Attribut` beim &lt;span> Element über die CSS Regel `background-color:${color}` einsetzen.

    ```javascript
    pointToLayer: function(feature, latlng) {
        let color = getColor(feature.properties.LT, COLORS.temperature);
        return L.marker(latlng, {
            icon: L.divIcon({
            className: "aws-div-icon",
            html: `<span>${feature.properties.LT.toFixed(1)}</span>`
            html: `<span style="background-color:${color};">${feature.properties.LT.toFixed(1)}</span>`
        })
    }
    ```

    COMMIT <https://github.com/webmapping/aws/commit/f56201074b8eaecbde7a704b2ba84224eb56003c>

## Wind Layer implementieren

- nach dem selben Muster wie bei der Temperatur, können wir in einer neuen Funktion `showWind` den Wind-Layer implementieren

    COMMIT <https://github.com/webmapping/aws/commit/f56201074b8eaecbde7a704b2ba84224eb56003c>

- noch besser sieht es aus, wenn wir ein Font Awesome Icon (`fa-solid fa-circle-arrow-down`) zur Anzeige der Windrichtung verwenden. Wir geben ihm einen neuen `className` (`.aws-div-icon-wind`), stylen es in CSS und rotieren es mit `transform`

    ```javascript
    let color = getColor(feature.properties.WG, COLORS.wind);
    return L.marker(latlng, {
        icon: L.divIcon({
            className: "aws-div-icon-wind",
            html: `<span><i style="transform:rotate(${feature.properties.WR}deg);color:${color}" class="fa-solid fa-circle-arrow-down"></i></span>`
        })
    })
    ```

- das CSS-Format kopieren wir von der Temperatur und setzen einen schwarzen, statt weißen Schatten

    ```css
    .aws-div-icon-wind span {
        font-size: 2em;
        font-weight: bold;
        padding: 0.3em;
        text-shadow:
            -1px -1px 0 black,
            -1px 1px 0 black,
            1px -1px 0 black,
            1px 1px 0 black;
        display: inline-block;
        transform: translate(-30%, -50%);
    } 
    ```

    COMMIT <https://github.com/webmapping/aws/commit/7b42869c42709de910eefef64b32a6f5177a27f3>

- die Windgeschwindigkeit können wir noch als `title`-Attribut setzen - damit wird es als Tooltip angezeigt

    COMMIT <https://github.com/webmapping/aws/commit/60737dab85db3ab56ecaf25551cea7cb32ff97e4>
