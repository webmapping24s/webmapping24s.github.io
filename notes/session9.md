# Session 9: Biketirol

## Repo vorbereiten

- Repo `biketirol` erstellen, lokal clonen, Template auspacken, `add` und `push` wie gehabt

    COMMIT <https://github.com/webmapping24s/biketirol/commit/e86fda6c08f9429c1598dd34fb3bb8ce7a8a62e3>

- GPX-Daten der Etappe besorgen: Links zur eigenen Etappe siehe `biketirol_etappen.ods` im OLAT-Kurs. Originalseite ansteuern und GPX-Track in `data/` mit dem Pattern `etappeNR.gpx` speichern dann `add` und `push`

    COMMIT <https://github.com/webmapping24s/biketirol/commit/16c3d3db5a3e7d18b1f31f608100fbc644cafab2>

- todo-Marker auflösen: straight forward

  COMMIT <https://github.com/webmapping24s/biketirol/commit/f165a007124007e12550a1eaf04ac3fcfb6ce44d>

- was ist neu im Template? **Gruppieren von TileLayern** über `L.layerGroup()` <https://leafletjs.com/reference.html#layergroup>

    ```javascript
    let eGrundkarteTirol = {
        sommer: L.tileLayer("https://wmts.kartetirol.at/gdi_summer/{z}/{x}/{y}.png", {
            attribution: `Datenquelle: <a href="https://www.data.gv.at/katalog/dataset/land-tirol_elektronischekartetirol">eGrundkarte Tirol</a>`
        }),
        nomenklatur: L.tileLayer("https://wmts.kartetirol.at/gdi_nomenklatur/{z}/{x}/{y}.png", {
            attribution: `Datenquelle: <a href="https://www.data.gv.at/katalog/dataset/land-tirol_elektronischekartetirol">eGrundkarte Tirol</a>`,
            pane: "overlayPane",
        })
    };
    L.control.layers({
        "eGrundkarte Tirol Sommer": L.layerGroup([
            eGrundkarteTirol.sommer,
            eGrundkarteTirol.nomenklatur
        ]).addTo(map)
    }).addTo(map);
    ```

## GPX - das GPS Exchange Format

- kennt wohl jede(r), gängiges Format zum Aufzeichnen von Tracks mit GPS, am Handy, etc.

- siehe <https://en.wikipedia.org/wiki/GPS_Exchange_Format> und Spezifikation <https://www.topografix.com/gpx.asp>

## Leaflet Plugin leaflet-elevation.js

- <https://github.com/Raruto/leaflet-elevation>

- siehe"*How to use*" für den Einbau der Plugin-Dateien .js und .css in index.html

    COMMIT <https://github.com/webmapping24s/biketirol/commit/de4cedb072d9f103fb70bac351f993db1abd39e2>

- Initialisieren des Plugins noch ohne Optionen in main.js

    ```javascript
    let controlElevation = L.control.elevation({}).addTo(map);
    controlElevation.load("data/etappe7.gpx");
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/42f29652702e5f2c2dcf292b4c3a719fa6f39462>

- das Resultat in der Karte:

    - eine Profillinie mit Steigung in Farben

    - Start- und Endpunkt in Grün/Rot

    - Kilometrierung im Verlauf

    - Höhenanzeige `onmouseover`

    - Eingangs-View auf Profil gesetzt

- dazu ein Höhenprofil unterhalb der Karte

    - `onmouseover` wird Position/Höhe in Karte angezeigt

    - der Ausschnitt wird synchronisiert

    - Profilausschnitte durch `mousedrag` auswählbar

    - Anzeige der Track-Statistik und Download-Möglichkeit

    - Höhenschieber rechts unten

    - ein-, ausklappbar

## Kosmetik über Optionen

- die Zeit in der Track-Statistik ausschalten - wir benötigen sie nicht

    ```javascript
    let controlElevation = L.control.elevation({
        time: false,
    }).addTo(map);
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/c94af2da841ed5726c744f87bdb3390f4b10dde4>

- den Profil DIV stylen

    - in index.html unter dem map-DIV einen neuen DIV mit der ID `profile`

    ```html
    <div id="profile"></div>
    ```

    - über die Optionen das Profil in diesen DIV schreiben und auf 300 Pixel Höhe setzen

    ```javascript
    let controlElevation = L.control.elevation({
        time: false,
        elevationDiv: "#profile",
        height: 300,
    }).addTo(map);
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/0b9b14fbb5f24cf20b74221106aeefb6c9b4bf22>

- den Profil DIV in `main.css` noch ähnlich wie die Karte stylen

    ```css
    #profile {
        margin-top: 0.3em;
        border: 1px solid gray;
    }
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/34b1f629b194d317e893375f85414c60f22d825f>

- Höhenprofil und Hintergrund über ein custom Theme stylen - der Name ist frei wählbar und wird als CSS-Klasse vom Plugin interpretiert

    ```javascript
    let controlElevation = L.control.elevation({
        // other options
        theme: bike-tirol
    }).addTo(map);
    ```

- Profil und Hintergrund über die Theme CSS-Klasse in main.css stylen

    - was ist **--ele-area** und **--ele-bg**? Das sind **CSS custom properties (variables)** <https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties>

    - beginnen mit `--` und können mit `var(--ele-area, Fallbackwert)` im Stylesheet verwendet werden

    - siehe <https://unpkg.com/@raruto/leaflet-elevation@2.4.0/dist/leaflet-elevation.css>

    ```css
    .bike-tirol {
        --ele-area: #3D9970;
        --ele-bg: white;
    }
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/4ff6326939160a2f72373a497ddfe3788c6293c5>

## Etappennavigation als Pulldown über ein Etappenobjekt

### ein Javascript Etappenobjekt erstellen

- Quelle ist die Tabelle der Etappenzuweisung im OLAT Kurs

- Sehr cool! **Online Convert CSV to JSON** <https://www.convertcsv.com/csv-to-json.htm> ansteuern

- Tabelleninhalt kopieren - aus dem Header werden `keys`, aus  den Werten `values`, jede Zeile wird ein Objekt im Array

- `etappen.js` erstellen und Ergebnis von CSV to JSON speichern - die Variablendeklaration einfach Vorne dazuschreiben

    ```javascript
    const ETAPPEN = [
        {
            "nr": 7,
            "user": "webmapping",
            "titel": "Scharnitz - Achensee",
            "weblink": "https://www.tirol.at/reisefuehrer/sport/radfahren/biketouren/a-bike-trail-tirol-scharnitz-achensee"
        },
        // mehr Etappen
    ];
    ```

- `etappen.js` in `index.html` Einbinden

    ```javascript
    <script src="etappen.js"></script>
    ```

COMMIT <https://github.com/webmapping24s/biketirol/commit/47855a48ae63e7101f6dab9e8c89e9ae964f245e>

### ein Pulldown-Menü oberhalb der Karte definieren

- in `index.html` oberhalb des map-DIVs das Formularelement &lt;select&gt; einbauen

    ```javascript
    <select id="pulldown"></select>
    ```

- innerhalb des &lt;select&gt;-Elements kommen &lt;option&gt;-Elemente mit einem `value`-Attribut und dem Anzeigetext

    ```javascript
    <select id="pulldown">
        <option value="etappe1">zur Etappen 1 ...</option>
        <option value="etappe7">zur Etappen 7 ...</option>
    </select>
    ```

- in `main-js` können wir dann mit `document.querySelector("#pulldown")` über die ID des &lt;select&gt;-Elements eine Referenz zum Pulldown speichern

    ```javascript
    let pulldown = document.querySelector("#pulldown");
    console.log("Pulldown: " , pulldown);
    ```

COMMIT <https://github.com/webmapping24s/biketirol/commit/762e9c5381e9479dce4964564519f1fb0a2049e7>

### das Pulldown mit einer for ... of Schleife und .innerHTML befüllen

- eine `for ... of` Schleife für das `ETAPPEN`-Objekt schreiben - bei jedem Schleifendurchgang ist das jeweilige Etappen-Objekt als Variable `etappe` verfügbar

    ```javascript
    for (let etappe of ETAPPEN) {
        console.log(etappe);
        console.log(etappe.nr);
        console.log(etappe.user);
        console.log(etappe.titel);
    }
    ```

- bei jedem Schleifendurchgang wird ein &lt;option&gt;-Element zum `.innerHTML` des Pulldowns hinzugefügt

    - **WICHTIG**: als value verwenden wir den/die github Userin (`user`) der jeweiligen Etappe

    - Nummer (`nr`) und Name der Etappe (`titel`) bilden den Label des Eintrag

    ```javascript
    for (let etappe of ETAPPEN) {
        pulldown.innerHTML += `
            <option value="${etappe.user}">Etappe ${etappe.nr}: ${etappe.titel}</option>
        `;
    }
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/7492cdadf443493a531a63d96cf88a20662479a2>

- die eigene Etappe sollten wir vorselektieren - das geht über das Attribut `selected` bei &lt;option&gt;

- wir setzen es mit einer **if-Abfrage** bei unserer Etappe auf `"selected"`, bei allen anderen auf `""`

    ```javascript
    for (let etappe of ETAPPEN) {
        let status = "";
        if ( etappe.nr == "7") {
            status = "selected";
        }
        pulldown.innerHTML += `
            <option ${status} value="${etappe.user}" ${status}>Etappe ${etappe.nr}: ${etappe.etappe}</option>
        `;
    }
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/4a0d58fe2a387705c44218baa5970e2ba484511b>

### bei Änderung im Pulldown zur Etappe wechseln

- immer wenn wir einen anderen Eintrag im Pulldown auswählen, wird ein **Event** ausgelöst - wir reagieren darauf mit einem **EventListener**

    ```javascript

    pulldown.onchange = function(evt) {
        console.log(evt, evt.target);
    }
    ```

- das mit dem `evt` kennen wir schon

    - in `evt.target` finden wir das Ziel der Änderung, also das Pulldown-Menü

    - in `evt.target.value` landet der aktuell selektierte Wert unseres `value`-Attributs, also der/die github Userin

- damit können wir die URL zur nächsten Etappen zusammensetzen

    ```javascript
    pulldown.onchange = function(evt) {
        let username = evt.target.value;
        let url = `https://${username}.github.io/biketirol`;
        console.log(url);
    }
    ```

- setzen wir `window.location.href` auf diese URL, wechselt der Browser dorthin

    ```javascript
    pulldown.onchange = function(evt) {
        let username = evt.target.value;
        let url = `https://${username}.github.io/biketirol`;
        window.location.href  = url;
    }
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/5095bfa8c88f4d21cad44323bf0b645ac0423001>

- mit `document`, `window` und `Events`, kann man alles, was man im Browser sieht (oder auch verborgen im Quelltext steht) ansteuern, lesen und ändern ....

- das Pulldown können wir natürlich auch mit CSS in `main.css` noch stylen

    ```css
    #pulldown {
        margin-bottom: 0.3em;
    }
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/c5611b1b2d526d6a4a12ce4abd6632394dbf9a63>

## zum Schluss noch eine Minimap für die Kartennavigation

- Plugin Leaflet.MiniMap: <https://github.com/Norkart/Leaflet-MiniMap>

- zuerst Script und Style in `index.html` einbauen

    ```html
        <!-- Leaflet Minimap -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet-minimap/3.6.1/Control.MiniMap.css" integrity="sha512-efbAfGnrnjA+hLwOLu91W034fBGPsMwZMVCTwLUI2PDX/m7rOiuhYZ+D2mZ8rKcpC/I/7pdgoL8T4eYvMHNoQg==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-minimap/3.6.1/Control.MiniMap.js" integrity="sha512-ceQPs2CHke3gSINLt/JV37W1rfJOM64yuH999hnRhTP7tNtcSBp5hlTKhn8CEIhsFweSBrZMPVotAKjoyxGWNg==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/9c39d88d59247422fbba2e98fbef92cc47d67337>

- dann das Plugin initialisieren, einen tileLayer für den Kartenhintergrund der Minimap bestimmen und über die Option `toggleDisplay` das Ein-, bzw. Ausklappen der Minimap ermöglichen

    ```javascript
    new L.Control.MiniMap(
        L.tileLayer("https://wmts.kartetirol.at/gdi_summer/{z}/{x}/{y}.png", {
            attribution: `Datenquelle: <a href="https://www.data.gv.at/katalog/dataset/land-tirol_elektronischekartetirol">eGrundkarte Tirol</a>`
        }), {
            toggleDisplay: true,
        }
    ).addTo(map);
    ```

    COMMIT <https://github.com/webmapping24s/biketirol/commit/e012d3bbab75d9a2e7bf24828b3a1d1276082aed>
