# Session 8: Wind & Wetter Beispiel

## Repo vorbereiten

- Repo `forecast` erstellen, lokal clonen, Template auspacken, `add` und `push` wie gehabt

    COMMIT <https://github.com/webmapping/forecast/commit/27503b6f6faef7147ec9d25b2a6c8e332eea8fe9>

- Icons besorgen: am Besten das Icons-Repo <https://github.com/metno/weathericons.git> temporär clonen und die SVG-Icons in das eigene, leere `icons/` Verzeichnis verschieben, dann `add` und `push`

    COMMIT <https://github.com/webmapping/forecast/commit/f1b1c43ef095e884892830ed00da730814e8e85f>

## Wettervorhersage met.no implementieren

### a) Daten asynchron in der Funktion showForecast laden und leeres Popup öffnen

- die Funktion zum Laden der Daten ist schon im Template verfügbar

    ```javascript
    // Wettervorhersage MET Norway
    async function showForecast(url) {
        let response = await fetch(url);
        let jsondata = await response.json();

        // aktuelles Wetter und Wettervorhersage implementieren
        console.log(jsondata);
    }
    showForecast("https://api.met.no/weatherapi/locationforecast/2.0/compact?lat=47.267222&lon=11.392778");
    ```

- danach laden wir mit `L.geoJSON()` die Daten und fügen dabei eine leere `pointToLayer` Option hinzu - auf den Marker verzichten wir damit, wir werden ihn durch ein **standalone Popup** ersetzen. Das `console.log` für die JSON-data kommentieren wir gleich aus

    ```javascript
    L.geoJSON(jsondata, {
        pointToLayer: function (feature, latlng) {

        }
    }).addTo(themaLayer.forecast)
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/ba5280ed7f30cdcba6de71c751f52bf77617b0c4>

- das **standalone Popup** erstellen wir mit `L.popup()` <https://leafletjs.com/reference.html#popup> und binden es nicht an einen Marker, sondern hängen es direkt mit einem leeren Template string an `themaLayer.forecast` - ein leeres Popup erscheint bei Innsbruck

    ```javascript
    L.geoJSON(jsondata, {
        pointToLayer: function (feature, latlng) {
            L.popup(latlng, {
                content: '<p>Hello world!<br />This is a nice popup.</p>'
            }).openOn(themaLayer.forecast);
        }
    }).addTo(themaLayer.forecast)
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/2d731d56365a97df503f0c9d2b6c2d752a861700>

- im Popup werden wir im nächsten Schritt die aktuellen Wetterwerte anzeigen und dazu müssen wir uns die JSON-Daten der Vorhersage genauer ansehen

### b) Popup mit den aktuellsten Werten erstellen

- in `pointToLayer` lassen wir uns vor dem Popup das Objekt `feature` anzeigen und erkennen die üblichen Attribute `feature.geometry` und `feature.properties`. Die Geometrie folgt dem alt bekannten Muster, aber die Properties sehen ganz anders aus - sie bestehen aus einem verschachtelten Objekt, das wir uns in VSCode und in der JSON-Vorschau des Browsers näher ansehen müssen.

    ```javascript
    console.log(feature.geometry);
    console.log(feature.properties);
    ```

- wir kopieren uns die JSON-Daten der Vorhersage für Innsbruck <https://api.met.no/weatherapi/locationforecast/2.0/compact?lat=47.267222&lon=11.392778>  (siehe Link beim Aufruf von `showForecast`) nach VSCode und analysieren nach *"F1 / Format document"* ihren Inhalt

- wir sehen ein GeoJSON `Feature`-Objekt vom Typ `Point` mit:

    - Koordinaten und Seehöhe in `feature.geometry.coordinates`

    - Metadaten der Attribute in `feature.properties.meta`

    - sowie den Vorhersage-Daten im Array `feature.properties.timeseries`

    - insgesamt gibt es 90 Vorhersage-Zeitpunkte für 9 Tage in die Zukunft

        - 1-stündlich: `feature.properties.timeseries[0]` - `feature.properties.timeseries[62]`

        - 6-stündlich: `feature.properties.timeseries[63]` - `feature.properties.timeseries[89]`

    - jeder Eintrag dieses Arrays besitzt:

        - einen Zeitstempel `time`

        - die vorhergesagten Werte in `data.instant.details`

        - sowie Wetteraussichten mit Wettersymbolen und voraussichtlichen Niederschlagsmengen für die drei Zeitpunkte `data.next_1_hours`, `data.next_6_hours`, `data.next_12_hours`

- wir beginnen mit dem Popup und erzeugen in einer Variablen `content` eine ungeordnete Liste mit Labeln und den Namen der Attribute in `feature.properties.meta`. Das Auflösen mit tatsächlichen Werten machen wir später. Statt dem "Hello world ..." Text verwenden wir die Variable `content` für den tatsächlichen Inhalt des Popups

    ```javascript
    let content = `
        <ul>
            <li>Luftdruck (hPa): air_pressure_at_sea_level</li>
            <li>Lufttemperatur (°C): air_temperature</li>
            <li>Bewölkungsgrad (%): cloud_area_fraction</li>
            <li>Niederschlag (mm): precipitation_amount</li>
            <li>Luftfeuchtigkeit (%): relative_humidity</li>
            <li>Windrichtung (°): wind_from_direction</li>
            <li>Windgeschwindigkeit (km/h): wind_speed</li>
        </ul>
    `;
    L.popup(latlng, {
        content: content
    }).openOn(themaLayer.forecast);
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/a55e0c42c6363656284a3d85bbf015a5a0308dee>

- zum Umsetzen der Attribut-Namen in tatsächliche werte müssen wir uns die Daten genauer ansehen. Die aktuellen Wetterwerte finden wir im ersten Eintrag (Index `0`) des `feature.properties.timeseries` Array und dort in `feature.properties.timeseries[0].data.instant.details` - ein *shortcut* zu diesen tief verschachtelten Details bietet sich an - wir definieren ihn am Beginn der `pointToLayer` Option:

    ```javascript
    let details = feature.properties.timeseries[0].data.instant.details;
    ```

- danach definieren wir in einer Variablen `content` mit Template-Syntax die aktuellen Wetterwerte als HTML-Code einer ungeordneten Liste. Den Wind rechnen wir in km/h um und runden auf ganze Zahlen mit `Math.round()`

    ```javascript
    let content = `
        <li>Luftdruck (hPa): ${details.air_pressure_at_sea_level}</li>
        <li>Lufttemperatur (°C): ${details.air_temperature}</li>
        <li>Bewölkungsgrad (%): ${details.cloud_area_fraction}</li>
        <li>Niederschlag (mm): ${details.precipitation_amount}</li>
        <li>Luftfeuchtigkeit (%): ${details.relative_humidity}</li>
        <li>Windrichtung (°): ${details.wind_from_direction}</li>
        <li>Windgeschwindigkeit (km/h): ${details.wind_speed}</li>
    `;
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/6c463f33ab1243ab7d90fe9903e99cd346e353f9>

- die Windgeschwindigkeit rechnen wir noch in km/h um und runden auf ganze Zahlen

    ```javascript
    `
        // ...
        <li>Windgeschwindigkeit (km/h): ${Math.round(details.wind_speed * 3.6)}</li>
    `
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/72b7a6088858b509decfeab253b993eeae0806d1>

- an dieser Stelle fällt auf, dass der Niederschlag immer `undefined` ist :-( Das liegt daran, dass er zwar in den Metadaten angeführt ist, in den Detaildaten aber fehlt. Nur bei den Wetteraussichten mit Wettersymbolen und voraussichtlichen Niederschlagsmengen `data.next_1_hours`, `data.next_6_hours`, `data.next_12_hours` wäre er zu finden, trotzdem verzichten auf eine Angabe im Popup und löschen diesen Eintrag aus der Liste. Ob es regnet oder nicht, werden wir später noch im Popzp sehen ...

    COMMIT <https://github.com/webmapping/forecast/commit/b197b71c434f92b95c198bdc8936d48cebd61838>

- wie schon beim Wetterstationen-Beispiel, können wir auch hier mit echten Datumsangaben arbeiten. Das Referenzdatum für die erste Vorhersage finden wir in `feature.properties.timeseries[0].time`. Wir initialisieren damit ein echtes Datum für den Zeitpunkt und schreiben ihn in der eingestellten Lokale als &lt;h4&gt; Element vor die Liste

    ```javascript
    let time = new Date(feature.properties.timeseries[0].time);
    let content = `
        <h4>Wettervorhersage für ${time.toLocaleString()}</h4>
        <ul>
            // Liste mit Werten
        </ul>
    `;
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/de43961c1dbfa279ab78fa62def8f23e387708ec>

### c) Wettersymbole für die nächsten 24 Stunden in 3 Stunden Abständen hinzufügen

- nachdem der `timeseries` Array aus 90 Einträge im Stunden-, bzw. 6-Stundenabstand besteht, können wir die Witterungsprognose für die nächsten 24 Stunden in 3 Stundenschritten implementieren. Wir verwenden dazu jeweils das Wettersymbol in `data.next_1_hours.summary.symbol_code` - die namentlich dazu passenden SVG-Icons liegen bereits im Unterverzeichnis `icons/`.

- dazu verwenden wir in eine **klassische for-Schleife** mit einer **Schleifenvariable** `i` die den `timeseries` Array abarbeitet. Nach jedem Schleifendurchlauf erhöhen wir die Zähler-Variable um `3` (Stunden) und beenden die Schleife beim Wert `24`.

    ```javascript
    for (let i = 0; i <= 24; i += 3) {

    }
    ```

- den Dateinamen des gewünschten Symbols finden wir im längsten verschachtelten Objekt, das wir bisher kennengelernt haben - in `feature.properties.timeseries[i].data.next_1_hours.summary.symbol_code` ;-)  Wir speichern ihn in einer Variablen `symbol`

    ```javascript
    // Wettericons für die nächsten 24 Stunden in 3-Stunden Schritten
    for (let i = 0; i <= 24; i += 3) {
        let symbol = feature.properties.timeseries[i].data.next_1_hours.summary.symbol_code;
    }
    ```

- jetzt müssen wir nur bei jedem Schleifendurchlauf das Popup um ein **img-Element** mit dem gewünschten Symbol erweitern. Nachdem die SVG-Icons im Original recht groß sind, verkleinern wir sie mit einem `style-Attribut` auf `32px` Breite

    ```javascript
    // Wettericons für die nächsten 24 Stunden in 3-Stunden Schritten
    for (let i = 0; i <= 24; i += 3) {
        // SVG-Icon zum Markup hinzufügen
        let symbol = feature.properties.timeseries[i].data.next_1_hours.summary.symbol_code;
        content += `<img src="icons/${symbol}.svg" alt="${symbol}" style="width:32px">`;
    }
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/d5ad4ef26068918e3c8831c3d8c355e294711188>

- zur besseren Lesbarkeit ergänzen wir bei den Symbolen das jeweilige Datum als Tooltip über ein `title-Attribut`. Das Datum finden wir in `feature.properties.timeseries[i].time` - wir speichern es in einer Variablen `time` und formatieren es in der eingestellten Lokale des Browsers

    ```javascript
    // Wettericons für die nächsten 24 Stunden in 3-Stunden Schritten
    for (let i = 0; i <= 24; i += 3) {
        let symbol = feature.properties.timeseries[i].data.next_1_hours.summary.symbol_code;
        let time = new Date(feature.properties.timeseries[i].time);
        content += `<img src="icons/${symbol}.svg" alt="${symbol}" style="width:32px" title="${time.toLocaleString()}">`;
    }
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/1193450be2c7e0a2b55d9318ffd546ef13b31a0e>

- zum Schluss ergänzen wir im Popup nach den Wettericons noch einen Download-Link zur JSON-Datei der Vorhersage. Was macht noch einmal das `target`?

    ```javascript
    // Link zum Datendownload
    content += `<p><a href="${url}" target="met.no">Daten downloaden</a></p>`;
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/5cbb05db03190752f9b6f84a2d3b5f9432d91c7e>

### d) Wettervorhersage für jeden Ort der Welt implementieren

- die Wettervorhersage für Innsbruck ist schon cool, aber noch cooler wäre, wenn wir Wettervorhersagen weltweit `onclick` hätten

- Leaflet's **Karten Events** machen das möglich - wir finden sie in der Leaflet Dokumentation bei *Map / Events / Interaction events* und dort unter `click` <https://leafletjs.com/reference.html#map-click>

- bei jedem Klick wird ein `MouseEvent` <https://leafletjs.com/reference.html#mouseevent> ausgesandt, in dem wir die Koordinaten des angeklickten Punkts in `latlng` erhalten

- mit der `.on()` Methode unseres `map` Objekts <https://leafletjs.com/reference.html#evented-on> können wir einen sogenannten **Event-Handler** definieren, der auf jeden Klick auf die Karte reagiert

    ```javascript
    map.on("click", function(evt) {
        console.log(evt);
    });
    ```

- im Event `evt` des Klicks finden wir mit `evt.latlng.lat` und  `evt.latlng.lng` die Koordinaten des angeklickten Punkts. Aus ihnen generieren wir die URL für <https://api.met.no/>. Der Aufruf der Funktion `showForecast` mit dieser URL platziert den Marker neu und holt sich die Vorhersage für den angeklickten Punkt. Der Event-Handler kommt ganz ans Ende unseres Skripts.

    ```javascript
    // auf Kartenklick reagieren
    map.on("click", function(evt) {
        showForecast(`https://api.met.no/weatherapi/locationforecast/2.0/compact?lat=${evt.latlng.lat}&lon=${evt.latlng.lng}`);
    });
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/ce4832501ed2e088c9b434f07cb9422886b77d05>

- Events können wir in Leaflet auch selbst auslösen - `map.fire` <https://leafletjs.com/reference.html#evented-fire> hilft uns dabei. Es erwartet die Art des Events und ein Event-Objekt um das wir uns selber kümmern müssen - wir füllen es mit dem von Oben bekannten `latlng` Eintrag, setzen als Koordinate Innsbruck und können somit den manuellen Aufruf von `showForecast` für Innsbruck auskommentieren. Das Popup wird trotzdem angezeigt, als hätten wir auf Innsbruck geklickt.

    ```javascript
    // Klick auf Innsbruck simulieren
    map.fire("click", {
        latlng: ibk
    });
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/19f4f86585835c1e1d634d2903d8b6a3b3457188>

## ECMWF Windvorhersage implementieren

Mit Wind ist immer auch Bewegung verbunden und deshalb werden wir im nächsten Schritt etwas Bewegung in unsere Karte bringen: animierte Windpfeile, wie sie einige von euch sicher schon aus Apps wie <https://windy.com> kennen

### a) Die Daten asynchron in der Funktion loadWind() laden

- die für unsere Windanimation benötigten Daten liegen auf dem Server der Geographie unter `wind-10u-10v-europe.json` <https://geographie.uibk.ac.at/data/ecmwf/data/wind-10u-10v-europe.json> und sind das Endprodukt einer Reihe von Schritten, die aus den Originaldaten beim ECMWF <https://confluence.ecmwf.int/display/UDOC/ECMWF+Open+Data+-+Real+Time> im Format GRIB2 die passende JSON-Datei erzeugt. Das "HOWTO ECMWF Windvorhersage-Daten für Leaflet velocity aufbereiten" <https://webmapping.github.io/forecast/ecmwf2json> zeigt den verwendeten Workflow

    ```javascript
    // Windkarte
    async function loadWind(url) {
        const response = await fetch(url);
        const jsondata = await response.json();
        console.log(jsondata);
    }
    loadWind("https://geographie.uibk.ac.at/data/ecmwf/data/wind-10u-10v-europe.json");
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/3a4873c0f39f6742c2ce8e9db7c9c42c1c8b3a8e>

- wir sehen in `jsondata` einen Array bestehend aus zwei Objekten:

    - `U-component_of_wind` <https://codes.ecmwf.int/grib/param-db/131>: *This parameter is the eastward component of the wind. It is the horizontal speed of air moving towards the east, in metres per second. A negative sign thus indicates air movement towards the west.*

    - `V-component_of_wind` <https://codes.ecmwf.int/grib/param-db/132>: *This parameter is the northward component of the wind. It is the horizontal speed of air moving towards the north, in metres per second. A negative sign thus indicates air movement towards the south.*

- aus diesen beiden Attributen kann später die Windrichtung und Windgeschwindigkeit berechnet werden. Jedes dieser Objekte besitzt ein `header`-  und `data`-Attribut. Im `header` stehen die Metadaten zur Erstellung der Vorhersage, die geographische Region für die die Daten gelten soll und die Art der Wind-Komponente. Im `data`-Attribut stehen die Werte von West nach Ost und Nord nach Süd.

### b) das Leaflet.velocity Plugin downloaden und einbinden

- bisher haben wir Plugin-Dateien immer von einer Online Ressource verlinkt - beim **Leaflet.velocity Plugin** und vielen anderen Plugins geht das nicht, deshalb laden wir uns in solchen Fällen die nötigen Plugin-Dateien direkt herunter und verwenden sie lokal. Folgende Schritte sind dazu nötig:

    - ein lokales `lib/` Verzeichnis erstellen
    - die Plugin-Seite von **Leaflet.velocity** <https://github.com/onaci/leaflet-velocity> ansteuern
    - unter "Releases" die letzte Version als .zip-Datei herunterladen
    - im `dist`-Ordner des Quellcodes die beiden Dateien `leaflet-velocity.css` und `leaflet-velocity.js` im neuen `lib/`-Verzeichnis entpacken
    - `add` und `commit` des `lib`-Verzeichnisses

    COMMIT <https://github.com/webmapping/forecast/commit/3b274103626e9ab21af698646c5a487c325d6b3b>

- damit ist das Plugin lokal verfügbar und wir können Stylesheet und Javascript in `index.html` einbinden

    ```html
    <!-- Leaflet velocity -->
    <script src="lib/leaflet-velocity.js"></script>
    <link rel="stylesheet" href="lib/leaflet-velocity.css">
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/a6f4d1e4735ae65ec1dbad7a3fecb536b879ba58>

### d) Das Plugin konfigurieren

- bevor wir das Plugin verwenden, müssen wir ein neues Overlay für den Wind-Layer erstellen und an die Layer-Control hängen

    ```javascript
    // thematische Layer
    let themaLayer = {
        forecast: L.featureGroup().addTo(map),
        wind: L.featureGroup().addTo(map),
    };

    // Hintergrundlayer
    let layerControl = L.control.layers({
        // Hintergrundlayer
    }, {
        "Wettervorhersage MET Norway": themaLayer.forecast,
        "ECMWF Windvorhersage": themaLayer.wind,
    }).addTo(map);
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/a22a9a4e6a7767f45a59fc5fd46c2b254de26847>

- danach können wir das Plugin initialisieren und dabei die JSON-Daten übergeben

    ```javascript
    // Velocity Plugin konfigurieren
    L.velocityLayer({
        data: jsondata
    }).addTo(themaLayer.wind);
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/784df52531407cd37f6ea4498290c29de91eb8db>

- **Voilà**, die Animation der Windrichtung und Stärke für Europa ist sichtbar. Wie das ganze funktioniert, kann man bei Visualizing wind using Leaflet - Wolfblog nachlesen (<https://wlog.viltstigen.se/articles/2021/11/08/visualizing-wind-using-leaflet/>). Noch beeindruckender ist die 3D-Visualisierung von <https://earth.nullschool.net/>.

- die Anzeige können wir noch verfeinern mit kräftigeren Windlinien über `lineWidth: 2` (ein leider undokumentiertes Attribut, das sich im Quelltext findet) und über ein `displayOptions`-Objekt das die Label der Datenanzeige übersetzt, die Windgeschwindigkeit in km/h umrechnet und die Control nach rechts unten verschiebt

    ```javascript
    L.velocityLayer({
        data: jsondata,
        lineWidth: 2,
        displayOptions: {
            directionString: "Windrichtung",
            speedString: "Windgeschwindigkeit",
            speedUnit: "k/h",
            position: "bottomright",
            velocityType: "",
        }
    }).addTo(themaLayer.wind);
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/56371eaca95a1a1e2db6f7af60cfec7df2f1c632>


### e) Den Zeitpunkt der Vorhersage ermitteln

- in jeder der beiden Wind-Komponenten der JSON-Daten finden wir (ident) die Zeit der Berechnung der Vorhersage (`refTime`) und den Zeitpunkt der Gültigkeit in Stunden (`forecastTime`) ab der Berechnung. Wir können uns aus der `refTime` wieder ein echtes Javascript-Datum erzeugen

    ```javascript
    // Vorhersagezeitpunkt ermitteln
    let forecastDate  = new Date(jsondata[0].header.refTime);
    ```

- für den Vorhersage-Zeitpunkt müssen wir dann nur noch die Zahl der Stunden in `forecastTime` dazu zählen. Dazu kommen die Javascript Methoden `Date.setHours()` <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/setHours> und `Date.getHours()` <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getHours> zum Einsatz. Sollte die aus der Addition resultierende Stundenkomponente größer als 23 sein, wird das Datum entsprechend auf den nächsten Tag erweitert.

    ```javascript
    // Vorhersagezeitpunkt ermitteln
    let forecastDate  = new Date(jsondata[0].header.refTime);
    forecastDate.setHours(forecastDate.getHours() + jsondata[0].header.forecastTime);
    console.log(forecastDate);
    ```

    COMMIT <https://github.com/webmapping/forecast/commit/943fe634fb8032b39ae824adde3721d27df318c0>

### f) Den Zeitpunkt auf der HTML-Seite anzeigen

- wir entscheiden uns, den Vorhersage-Zeitpunkt im Footer anzuzeigen und fügen nach der Quelle der ECMWF Daten ein &lt;span&gt;-Element mit der ID `forecast-date` ein

    ```html
    <a href="https://www.ecmwf.int/en/forecasts/datasets/open-data">ECMWF Open Data</a>
    <span id="forecast-date"></span>
    ```

- dann "holen" wir uns in `main.js` mit `document.querySelector(#forecast-date)` dieses &lt;span&gt;-Element und setzten seinen Inhalt mit `.innerHTML` auf den ermittelten Vorhersage-Zeitpunkt in der eingestellten Lokale

    ```javascript
    document.querySelector("#forecast-date").innerHTML = forecastDate.toLocaleString();
    ```

    COMMIT <>

- mit Hilfe eines Template-Strings ergänzen wir noch einen Link zu den JSON-Daten

    ```javascript
    document.querySelector("#forecast-date").innerHTML = `
        (<a href="${url}" target="met.no">Stand ${forecastDate.toLocaleString()}</a>)
    `;
    ```

- damit ist die Karte fertig

    COMMIT <https://github.com/webmapping/forecast/commit/09611a17f5c573ce9afb93f486a44fd78e8c334c>

### document.querySelector() und .innerHTML ...

So unspektakulär dieser letzte Schritt aussehen mag, so spektakulär sind die Möglichkeiten, die uns `document.querySelector()`  und `.innerHTML` in Kombination bieten, denn damit können wir alles auf der HTML-Seite aus Javascript heraus beeinflussen. Mit alles ist dabei wirklich alles gemeint, denn wir könnten die beiden auch so einsetzen:

```javascript
document.querySelector("#map").innerHTML = "heute keine Karte";
```

oder so

```javascript
document.querySelector("body").innerHTML = "das war's für heute";
```

aber besser ist's, wir kommentieren die beiden letzten Zeilen wieder aus :-)

COMMIT <https://github.com/webmapping/forecast/commit/92f64dcb61e67338970d1f3deb20fe4b064675e6>
