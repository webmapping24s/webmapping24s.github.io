# Session 4: Javascript und GeoJSON

## Inhalte der Workload Tutorials

### Tutorial 1 - Introduction to Javascript

<https://www.codecademy.com/courses/introduction-to-javascript/lessons/introduction-to-javascript/>

- `console.log(5)` als klassisches Beispiel für ein **Objekt** (`console`) mit **Methode** (`.log()`) und **Argument** (`5`)

- zur Frage **Semikolon** am Ende der Zeile Ja/Nein?  Ich würde sagen Ja, zur Sicherheit

- **Kommentare**: einzeilig mit `//`, mehrzeilig mit `/* */`

    - TIPP: "*F1 Add / Remove Line comment*" ist sehr praktisch mit "*STRG+K,C*" und "*STRG+K,U*"

- **Datentypen**: `string`, `number`, `boolean`, `null`, `undefined`, `objects`

- **Anführungszeichen** bei Strings: single / double quotes? Was ist besser? Es kommt darauf an, in jedem Fall ist `concatenation` mit `+` sehr mühsam und Fehler anfällig. **Viel besser ist ...**

- **String interpolation mit Template literals - wir nennen sie ab jetzt Template Strings**

    - **!!!Wichtig**: wir werden sie sehr oft verwenden

    - sogenannte "Backticks" \` als Begrenzer des Strings - ermöglichen auch Zeilenumbrüche innerhalb des Strings

    - Variablen innerhalb des Strings können über `${variable}` Interpolation direkt verwendet werden

    ```javascript
    let age = 5;
    console.log(`I am ${age} years old`);
    console.log(`Next year I'll be ${age + 1} years old)`);
    ```

- **Math operators**: `+`, `-`, `*`, `/`, `%` (Modulo)

- **Properties**: `.length` für die Länge eines Strings z.B.

- **Methods**: sind durch Punkt vom Objekt davor getrennt und müssen mit `()` aufgerufen werden, z.B. `.toUpperCase()`, `.startswith("H")`, `.trim()`, MDN zeigt noch viel mehr (z.B. [String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String), [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number))

- **Objects**: z.B. `Math.random()`, `Math.floor()`, `Math.ceil()`, `Number.isInteger(2024)`

### Tutorial 2 - Variables

<https://www.codecademy.com/courses/introduction-to-javascript/lessons/variables/>

- **var** seit Urzeiten von Javascript

- **let**, **const** seit [ECMAScript 6th Edition](https://262.ecma-international.org/6.0/)

    - Variablen sind wie "*Boxen mit einem Label und einem Wert*" -> den Label können wir frei wählen (z.B. `meinName`), er darf nur keine Zahlen am Anfang haben, Leerzeichen beinhalten, oder ein [reserved word](https://262.ecma-international.org/6.0/#sec-reserved-words) sein (z.B. `function`, etc.)

    ```javascript
    let meinName = "Max"
    ```

    - Bestandteile der Variablendeklaration:

        - `let` -> Keyword
        - `meinName` -> Variablenname
        - `=` -> Assignment operator
        - `"Max"` -> Wert

    - Variablen sind case sensitiv! `nz` != `NZ` ... oft verwendet man [camelCase](https://en.wikipedia.org/wiki/Camel_case)

- **Math assignement operators**: `+=`, `-=`, `*=`, `/=` ... für uns nicht so wichtig

- **Increment und Decrement**: `++`, `--` ... brauchen wir öfter

- **typeof**: nicht so wichtig für uns, warum mit Leerzeichen? Weil es ein Operator ist ...

So viel zu den Tutorials, wenden wir uns wieder dem Neuseelandbeispiel zu und verwenden ein paar der gelernten features

## main.js umbauen, Teil 1

- verwenden wir `let` statt `var`
    
    COMMIT: <https://github.com/webmapping24s/nz/commit/f1c5f236be8a5a1e02a2ab522766cedabf7c59a3>

- speichern wir ganz oben im Skript die Breite und Länge unserer Koordinate in zwei Variablen `lat` und `lng` und verwenden wir `lat`, `lng` bei `setView()` und bei `L.marker()`

    COMMIT: <https://github.com/webmapping24s/nz/commit/df402b9beb7983867709f64cab6d170f859579f9>

- speichern wir den Zoom-Faktor in einer Variablen `zoom` und verwenden ihn bei `setView()`

    COMMIT: <https://github.com/webmapping24s/nz/commit/ffd76d79663057ddf3d6cddda1d2cffdae4126ff>

- verwenden wir **Template strings** beim Popup, machen wir den HTML Code lesbarer und zeigen die Koordinate über **String interpolation** an

    ```html
    marker.bindPopup(`
        <h2>Tongariro Nationalpark</h2>
        <ul>
            <li>Breite: ${lat}</li>
            <li>Länge: ${lng}</li>
        </ul>
    `).openPopup();
    ```

    COMMIT: <https://github.com/webmapping24s/nz/commit/699359cca3b5301a2e509855ec2e5cdab5816fff>

- lernen wir eine neue Methode `.toFixed(Zahl)` bei Dezimalzahlen kennen und verwenden wir sie direkt innerhalb der *String interpolation*

    COMMIT: <https://github.com/webmapping24s/nz/commit/1f2f6a99c55f3774ab4afde480b8123a48bbc874>

## Leaflet Dokumentation entdecken

Das **L** steht für Leaflet und unter <https://leafletjs.com/reference.html> steht, was wir mit diesem **L** so machen können

### L.map zum Initialisieren der Karte

- nehmen wir uns die Zeile `let map = ...` vor und suchen in der Dokumentation nach `L.map`

- ganz oben werden wir fündig: <https://leafletjs.com/reference.html#map>

    **!TIPP**: immer wenn beim Mouseover in den Leaflet-Docs Anchor Symbole erscheinen, können wir sie als URL kopieren und direkt dorthin verweisen

- allerdings steht hier unter "*Usage example*" etwas anderes als bei uns - wir ersetzen unsere Version mit der Version der Dokumentation

    ```javascript
    let map = L.map('map', {
        center: [lat, lng],
        zoom: zoom
    });
    ```

    COMMIT: <https://github.com/webmapping24s/nz/commit/f93ae1279b27c7668a357bb3bf8c0d0842ba5dde>

- unter "*Creation*" steht die Erklärung wie man `L.map` verwenden kann -> "*Factory / Description*"

    - z.B. `L.map(<String> id, <Map options> options?)`

    - das mit `<String> id` kennen wir schon, dort steht bei uns "*map*" als ID des Karte-DIVs

    - alternativ dazu, könnten wir in Zeile zwei auch ein `<HTMLElement> el` angeben, was soviel heißt wie eine Referenz auf den Karten-DIV - klingt kompliziert, ist es aber nicht und auch wenn wir es jetzt noch nicht brauchen, kann der Code für das Erstellen so einer Referenz nicht schaden (mehr dazu später):
    
        ```javascript
        let kartenElement = document.querySelector("#map")

        // und dann können wir diese Variable statt dem String `map` verwenden
        let map = L.map(kartenElement, {
            center: [lat, lng],
            zoom: zoom
        });
        ```

    - das mit den `<Map options> options?` ist neu und bedeutet, dass wir zusätzlich noch Optionen für die Karte definieren können. Die Kartenoptionen sind optional - deshalb das Fragezeichen bei `options?`. Wenn wir allerdings Optionen angeben, müssen sie als sogenannte *object literals* übergeben werden. Was ist das?

**Einschub object literals**

- *object literals* sind Sammlungen von Key/Value Paaren mit spezieller Syntax

- sie beginnen und enden mit geschwungenen Klammern `{}`

- sie bestehen aus beliebig vielen, Komma getrennten "*Key-Value*" Paaren

- *Key* und *Value* werden durch einen Doppelpunkt (`:`) getrennt

- "*Keys*" sind einfache, sprechende Zeichenketten ohne Sonderzeichen (vorerst ...)

- "*Values*" können alles sein, von Werten bis hin zu weiteren Objekten

    ```javascript
    OBJEKT = {
        key1: value1,
        key2: value2
    }
    ```

- der Zugrif auf die Einträge des Objekt erfolgt über sogenannte *Punktnotation* - `OBJEKT.key1` enthält dabei `value1` und `OBJEKT.key2` enspricht `value1`. Sind die Werte selber wieder Objekte, kommen mehrere Punkte zum Einsatz - z.B.

    ```javascript
    let punkt = {
        kooridnate: {
            x: 10,
            y: 20
        }
    }
    console.log(punkt.kooridnate.x): // 10
    console.log(punkt.kooridnate.y): // 20
    ```

- eine spezielle Notation steht noch beim `center` -> ein sogenannter "*Array*"

**Einschub Array**

- *Arrays* beinhalten Komma getrennte Listen von Werten

- sie beginnen und enden mit eckigen Klammern `[]`

- die Werte können wieder alles sein, also Strings, Zahlen, aber auch Objekte oder neue Arrays

    ```javascript
    ARRAY = [ value, anothervalue, ...]
    ```

    - der Zugriff auf die Werte des Arrays erfolgt über Indices in eckigen Klammern, wobei der Index für das erste element `0` und nicht `1` ist, also ARRAY[0] für `value` und ARRAY[1] für `anothervalue`. Die Anzahl der Elemente eines Arrays liefert unser `ARRAY.length`


**weiter in der Leaflet Dokumentation von L.map**

- in der Dokumentation stehen noch weitere Sektionen:

    - **Options** wie z.B. unser `center` (<https://leafletjs.com/reference.html#map-center>) und `zoom` (https://leafletjs.com/reference.html#map-zoom)

    - **Methods** wie z.B. unser (altes) `setView` (<https://leafletjs.com/reference.html#map-setview>)

    - alles andere wird uns (vielleicht) später beschäftigen

**Was haben wir in unserer Karte noch verwendet?**

### L.tileLayer für den Kartenhintergrund

- <https://leafletjs.com/reference.html#tilelayer>

- <https://leafletjs.com/reference.html#tilelayer-addto> (versteckt in "*Methods inherited from Layer*")


### L.marker für den Marker mit Popup

- <https://leafletjs.com/reference.html#marker>

- <https://leafletjs.com/reference.html#marker-addto> (versteckt in "Methods inherited from Layer")

- <https://leafletjs.com/reference.html#marker-bindpopup> (versteckt in "Popup methods inherited from Layer")

- <https://leafletjs.com/reference.html#marker-openpopup> (versteckt in "Popup methods inherited from Layer")


## Maßstab implementieren

Probieren wir ein neues Features mit Hilfe der Dokumentation zu implementieren - einen Massstab für die Karte. Er soll metrisch sein und nur das ...

- <https://leafletjs.com/reference.html#control-scale>

    ```javascript
    L.control.scale({
        imperial: false
    }).addTo(map);
    ```

    COMMIT: <https://github.com/webmapping24s/nz/commit/a316b8c84ef2d6b679f310ac9b75e58727820fcf>

Jetzt ist ein guter Zeitpunkt, Version 1.0 unseres Neuseeland Beispiels auf github.com zu veröffentlichen

## Release Neuseelandreise v1.0

- wir pushen alles zu github.com

- wir loggen uns bei github.com ein und wechseln in unser Repository nz

- bei "*Releases*" in der rechten Spalte wählen wir den Link "*Create a new release*"

- dann komplettieren wir die Angabe zum Release:

    - bei "*Choose a tag*" geben wir "v1.0" ein und drücken "*+ Create new tag: v1.0*"

    - "*Release title*" "1. Version der Neuseelandreise"

    - "*Describe this release*" "Etappenseite mit Bildern, Texten, Links, Navigation zu den Nachbar:innen und einer einfach Übersichtskarte mit Marker, Popup und Maßstab"

    - Ergebnis: <https://github.com/webmapping24s/nz/releases/tag/v1.0>


## Geometrie für die Karte

### L.geoJSON()

- bevor wir loslegen, löschen wir den Marker Code mit Popup aus - der Marker verschwindet von der Karte ...

- GeoJSON ist unser Mittel zur Wahl, wenn wir Geometrie mit Attributen in den Karten benötigen

- Spezifikation: <https://geojson.org/>

- wir verwenden das Objekt dort, bauen es für uns um und speichern es in einer Variablen `jsonPunkt` - **ACHTUNG**: lat/lng sind bei GeoJSON vertauscht ...

    ```javascript
    let jsonPunkt = {
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [lng, lat]
        },
        "properties": {
            "name": "Tongariro Nationalpark"
        }
    };
    ```

    - dann fügen wir die Leaflet Methode `L.geoJSON()` hinzu, übergeben ihr den `jsonPunkt` und hängen ihn an die Karte

        ```javascript
        L.geoJSON(jsonPunkt).addTo(map);
        ```

    - die Dokumentation bei Leaflet zeigt, wie wir `L.geoJSON()` verwenden - <https://leafletjs.com/reference.html#geojson>

        - das Optionen-Objekt mit `style` brauchen wir noch nicht - wir löschen es und damit sind unsere Optionen nur noch ein leeres Objekt `{}`

    - `bindPopup()` verwenden wir schon und bauen uns ein Popup mit Koordinatenanzeige. `bindPopup()` wird dabei eine Funktion mit dem Argument `layer` übergeben in dem unser GeoJSON Punkt landet.

    - wie sprechen wir die Koordinaten und Attribute dieses GeoJSON Punkts in der Variablen `layer` an? Nachdem unser GeoJSON Objekt vom Typ Feature ist, können wir es unter `layer.features` ansprechen. Die Koordinaten landen darunter als Array `coordinates` in einem Objekt `geometry` und der Titel als `name` im Objekt `properties` - wir können uns diese Wert in der Konsole anzeigen lassen

        ```javascript
        console.log(layer.feature);                           // das ganze GeoJSON Objekt des Punkts
        console.log(layer.feature.geometry.coordinates[0]);   // Länge
        console.log(layer.feature.geometry.coordinates[1]);   // Breite
        console.log(layer.feature.properties.name);           // Etappentitel
        ```

    - damit können wir Etappentitel und Koordinaten in unserem Popup als **Template string** einbauen und über `return` an das Kartenscript zurückgeben, das sich darum kümmert, dass das Popup mit  den richtigen Werten beim Marker angezeigt wird

        ```javascript
        L.geoJSON(jsonPunkt, {}).bindPopup(function (layer) {
            return `
                <h2>${layer.feature.properties.name}</h2>
                <ul>
                    <li>Breite: ${layer.feature.geometry.coordinates[1].toFixed(5)}</li>
                    <li>Länge: ${layer.feature.geometry.coordinates[0].toFixed(5)}</li>
                </ul>
            `;
        }).addTo(map);
        ```
    - COMMIT: <https://github.com/webmapping24s/nz/commit/e2ab10297b4e5dd25448ae301fee3bf6acbe5291> 

Auf den ersten Blick, scheint das alles sehr kompliziert, aber der Vorteil des Codes, den wir entwickelt haben ist, dass es jetzt ganz einfach ist, alle Etappenmarker in die Karte einzubauen. Wir benötigen nur ein geoJSON Objekt mit allen Markern und das erzeugen wir jetzt

### Alle Etappenmarker in der eigenen Karte implementieren

- im OLAT Kurs finden wir in `etappen.ods` eine Tabelle für alle Etappen mit den Spalten Nummer `nr`, Etappenuser `user`, Etappenname `name`, geographische Breite `lat`, geographische Länge `lng` und dem Link zur Wikipedia-Seite `wikipedia` . Sie wurde aus euren Beispielen durch Copy/Paste generiert. Wir öffnen sie mit **LibreOffice**

- diese Tabelle können wir im Netz in GeoJSON umwandeln - Google-Suche "*Convert CSV to GeoJSON*" bringt uns zu

- <https://www.convertcsv.com/csv-to-geojson.htm>

    - Tabelleninhalt in das Feld bei Step 1 kopieren und fast fertig, denn

    - wir bekommen einen Fehler: "*Please enter the Latitude and Longitude Field #s*"

    - `lat/lng` wird nicht erkannt und das liegt nicht an den Spaltenüberschriften, sondern an den Kommas (",") bei den Koordinaten, denn das Konvertierungs-Tool erwartet einen Punkt (".") als Komma - in LibreOffice können wir das bei den Spalten `lat` und `lng` mit "*Format / Zellen / Zahlen / Sprache*" einstellen

    - danach noch einmal Copy/Paste und fertig ist unser GeoJSON

    - Step 4 beinhaltet das Resultat: wir speichern es in einer Variablen `jsondata`. Die Koordinaten landen als Array `coordinates` in einem Objekt `geometry` und unsere Spaltenüberschriften sind jetzt die Keys der `properties` mit den entsprechenden Werten. Wollen wir auf die einzelnen Werte in unserem `bindPopup` zugreifen können wir das mit:

        ```javascript
        console.log(layer.feature);                           // das jeweilige Etappenobjekt
        console.log(layer.feature.geometry.coordinates[0]);   // Länge
        console.log(layer.feature.geometry.coordinates[1]);   // Breite
        console.log(layer.feature.properties.nr);             // Etappennummer
        console.log(layer.feature.properties.user);           // github-User:in
        console.log(layer.feature.properties.name);           // Etappentitel
        console.log(layer.feature.properties.wikipedia);      // Wikipedia-Link
        ```

- jetzt müssen wir nur noch das `jsondata` Objekt mit allen Etappen  beim Aufruf von `L.geoJSON` verwenden und alle Marker sind in der Karte mit einem Popup sichtbar

    COMMIT: <https://github.com/webmapping24s/nz/commit/f15b8330504db97a8ab995fa8c0a2fb976bf12ee>

- das Popup verfeinern wir schließlich noch und ergänzen über die Properties `user` und `wikipedia` zwei Links zur Etappen- und Wikipediaseite

    ```javascript
    `
        <li><a href="https://${layer.feature.properties.user}.github.io/nz">Etappenseite</a></li>
        <li><a href="${layer.feature.properties.wikipedia}">Wikipediaseite</li>
    `
    ```

COMMIT: <https://github.com/webmapping24s/nz/commit/9c4550d93edd33456e3cd28e46b278d13f113fd5>

## Release Neuseelandreise v1.1 kann kommen :-)

<https://github.com/webmapping24s/nz/releases/tag/v1.1>
