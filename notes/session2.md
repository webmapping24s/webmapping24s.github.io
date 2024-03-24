# Session 2 - HTML Struktur & GIMP (14.3.2024)

## zuvor noch kurz zu den Tutorials

- neue Elemente: &lt;br> für "break" (=weicher Zeilenumbruch), &lt;ol> für "ordered list" und &lt;video>

- neues id-Attribut kann bei jedem Element verwendet werden, muss eindeutig sein und kommt später ...

- Kommentare mit &lt;!-- comment -->

- wenn man explizit Kleiner--als-Zeichen oder Größer-als-Zeichen will, dann &amp;gt; &amp;lt; verwenden

- target="_blank" ist ein 2-schneidiges Schwert: stelle dir vor, jeder Link im Internet geht in einem neuen Fenster auf ;-) Wir einigen uns darauf, dass wir es vorerst nicht verwenden

- &lt;span>, &lt;div> als Strukturelement, es gibt noch mehr davon ...

## Hauptstruktur für die Etappenseite

Wir geben der Seite Struktur und verpacken alles im &lt;body> in in &lt;main> und &lt;article>

```html
<body>
    <main>
        <article>
            <!-- unser bisheriger Content -->
        </article>
    </main>
</body>
```

- "*F1 / Format Document*" verwenden um die Einrückungen zu korrigieren

- COMMIT: <https://github.com/webmapping/nz/commit/48231d7ff7be01a9126bae5b04511187331e5533>

## Navigation verschieben

- den &lt;nav>-Block ganz Unten schieben wir in ein &lt;footer>-Element

    ```html
    <body>
        <main>
            <! ... -->
        </main>
        <footer>
            <nav>
                <! ... -->
            </nav>
        </footer>
    </body>
    ```

    - wieder "*F1 / Format Document*" verwenden

    - COMMIT: <https://github.com/webmapping/nz/commit/69c08c4a22976f2498ece518699e3836a96bcd05>

## Header erstellen mit zwei Bildern

### Bannerbild 1280x365px

- bei Pixabay nach "*Tongariro*" suchen - <https://pixabay.com/de/photos/tongariro-vulkan-herr-der-ringe-583179/>

    - Version mit 1920 Pixel Breite downloaden

    - landet bei uns im Download-Ordner ...

- Bild zuschneiden mit <https://gimp.org/>

    - Breite auf 1280 Pixel bringen

        - Bild → Bild skalieren → 1280x832 Pixel einstellen

        - TIPP: in den Eingabefeldern könnte man auch rechnen ...

    - Bild Oben und Unten abschneiden

        - Bild → Leinwandgröße  → 1280x365 Pixel einstellen

        - Ebene → Ebene auf Bildgröße

        - Datei → Exportieren nach → `images/header.jpg` → Exportieren

            - Qualität: 90
            - Vorschau speichern wegklicken
            - EXIF, XMP, IPTC wegklicken

    - Bild erscheint im images-Verzeichnis von Vscode

    - COMMIT: <https://github.com/webmapping/nz/commit/cd7f44194ad233f4e9995a630f9d10a41963c711>

### User-Bild 100x100px

- bei Pixabay nach "*Bus*" suchen - bringt den Bully - <https://pixabay.com/photos/vw-bus-vw-bully-volkswagen-sea-1845719/>

    - Vorschaubild speichern unter `images/user.jpg`

    - Bild wird im images-Verzeichnis von Vscode angezeigt

- Größe ändern auf 100x100 mit <https://gimp.org/>

    - Doppelklick auf Symbol "rechteckige Auswahl" oder Werkzeuge → Auswahlwerkzeuge → Rechteckige Auswahl (R)

        - Seitenverhältnis → Fixiert → 1:1

        - Rahmen aufziehen und mit Drag positionieren

        - Position und Größe werden angezeigt, auch der Bildmittelpunkt

        - Größe kann auch geändert werden an den Ecken

        - wenn alles passt ...

    - Bild → Auf Auswahl zuschneiden

    - Bild → Bild skalieren → 100x100 → Skalieren

        - Auflösung ... was ist das? Im Web egal, 72, 300 bei Druck

        - Datei → `user.jpg` überschreiben

            - Qualität: 100 (ist ja klein)

            - Vorschau speichern weg

            - EXIF, XMP, IPTC weg

    - COMMIT: <https://github.com/webmapping/nz/commit/b0a046d45e3f9fa602c82808548bd30d2a30b7d8>

### Bilder im Header einbauen

- ein &lt;header>-Element oberhalb von &lt;main> als Strukturelement

    ```html
    <body>
        <header>
        </header>
        <main></main>
        <footer></footer>
    </body>
    ```

- Einbau der Bilder diesmal ohne &lt;figure>-Element

    - Bannerbild als &lt;img>

    - Userbild als &lt;a>-Link auf das eigene github.com Repo mit &lt;img>

    ```html
    <header>
        <img src="images/header.jpg" alt="Blick auf den Tongariro mit Straße im Vordergrund">
        <a href="https://github.com/webmapping/nz"><img src="images/user.jpg" alt="VW Bus auf Wiese"></a>
    </header>
    ```

    COMMIT: <https://github.com/webmapping/nz/commit/f9e458b18b8cb439108431be26bd9cf38b7b1f0a>

- Bildquellen im Header als Kommentare hinzufügen

  COMMIT: <https://github.com/webmapping/nz/commit/4633622394f42e9f1f4bffcc1f470e28566cb2de>

## HTML Üben: "Top Spots der Webmapper:innen"

- Repo `top` - erstellen, lokal clonen, online bringen
- `index.html`: selbe Seite wie die Etappe der Neuseelandreise nur mit anderen, beliebig wählbaren Inhalten
    - Texte von Wikipedia oder eigene
    - Bilder von <https://pixabay.com>, <https://commons.wikimedia.org> oder eigene
        - `images/header.jpg`:    1280 * 365
        - `images/user.jpg`:       100 * 100
        - `images/attraction.jpg`: 900 * NNN (Querformat)

    - Navigation zu selben Nachbar:innen wie bei Neuseelandreise

- COMMITS siehe <https://github.com/webmapping/top/commits/main/>

- unsere erstellte Seite: <https://webmapping.github.io/top> (Online), <https://github.com/webmapping/top> (Repo)

## Leaflet Erstkontakt

- eine Übersichtskarte erstellen nach dem Leaflet Tutorial unter <https://leafletjs.com/examples/quick-start/>

1. Leaflet CSS und Script einbauen

    - Stylesheet als &lt;link>-Element

    - Skript als &lt;script>-Element

    ```html
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin="" />

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
    ```

   COMMIT: <https://github.com/webmapping/top/commit/46983766b8e8ce07d92499b75d9aad1f349ee6d4>

2. Kartenfeld definieren

    - Überschrift als &lt;h3>

    - Kartenbereich als &lt;div> mit

        - einem id-Attribut um die Karte aus dem Skript heraus ansprechen ("programmieren") zu können

        - einem style-Attribut mit CSS Regeln für die Größe des Kartenfelds - mehr zu CSS später

    ```html
    <h3>Übersichtskarte</h3>
    <div id="map" style="width:90%;height:600px"></div>
    ```

   COMMIT: <https://github.com/webmapping/top/commit/80f4a4eb9c8672f42be066c0fd649faad46ac77b>

3. Kartenskript für eine Karte mit OpenStreetMap im Hintergrund und einem Marker mit geöffnetem Popup

    - wir verwenden die Leaflet-Bibliothek, die wir in der Variablen **L** finden

    - Task 1: mit `L.map('map')` erzeugen wir im &lt;div> mit der ID "map" eine Leaflet-Karte und blicken über `.setView()` auf die Koordinaten in geographischer Breite/Länge im Zoomlevel 13. Diese Karte speichern wir in einer Variablen `var map` damit wir später noch Features hinzufügen können.

        ```javascript
        var map = L.map('map').setView([47.908683, -124.636604], 13);
        ```

    - Task 2: mit `L.tileLayer()`  erzeugen wir einen Hintergrundlayer der OpenStreetMap, setzen mit `maxZoom` den maximalen Zoomfaktor auf 19 und geben in `attribution` die Quelle des Layers an. Mit `.addTo(map)` wird der Hintergrundlayer an die Karte gehängt - wir sehen ihn jetzt im Browser.

        ```javascript
        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
        }).addTo(map);
        ```

    - Task 3: `L.marker()` definiert einen Marker an der gewünschten Koordinate und hängt ihn mit `.addTo(map)` an die Karte. Damit wir noch ein Popup hinzufügen können, merken wir uns diesen Marker in der Variablen `var marker`. Der Marker wird im Browser in der Karte angezeigt.

        ```javascript
        var marker = L.marker([47.908683, -124.636604]).addTo(map);
        ```

    - Task 4: mit `marker.bindPopup()` definieren wir beim Marker ein Popup mit HTML-Inhalt und öffnen es mit `.openPopup()`. Das geöffnete Popup wird im Browser beim Marker in der Karte angezeigt.

        ```javascript
        marker.bindPopup("<h2>Hello world!</h2><br>I am a popup.").openPopup();
        ```

    - alles zusammen

        ```html
            <script>
            var map = L.map('map').setView([47.908683, -124.636604], 13);

            L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
                maxZoom: 19,
                attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
            }).addTo(map);

            var marker = L.marker([47.908683, -124.636604]).addTo(map);
            marker.bindPopup("<h2>Hello world!</h2><br>I am a popup.").openPopup();
        </script>
        ```

    - COMMIT: <https://github.com/webmapping/top/commit/ac9571b880e8537a6c015f3a7cc4cd9f69f5ef1e>

**das war's für heute ...**
