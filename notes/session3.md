# Session 3 - CSS Quiz & Leaflet first map (21.3.2024)

## CSS entdecken mit einem Quiz

### Stylesheet einbinden

- wir haben ein Stylesheet vorbereitet unter <https://github.com/webmapping/nz/blob/main/main.css>

- über "*Raw*" kann der Code kopiert werden

- in `nz/index.html` im &lt;head> Bereich ein &lt;link> Element erzeugen und als href `main.css` eintragen

    ```html
    <link rel="stylesheet" href="main.css">
    ```

- STRG+Klick auf `main.css` ermöglicht uns, `main.css` an der richtigen Stelle zu erstellen

- dann den Inhalt unserer Vorlage dorthin kopieren, speichern und eure Seite sollte anders aussehen ;-)

- COMMIT `main.css`: <https://github.com/webmapping/nz/commit/2ae77c4b403c984610e85c9500d824bfeda0efe0>

- COMMIT &lt;link>: <https://github.com/webmapping/nz/commit/a9114b62c3c645dcca6dfbf5fbb606863666f355>

### CSS Quiz in Dreierteams und einer halben Stunde

- Vorgaben und Fragen sind hier: <https://webmapping.github.io/nz/quiz>

    - COMMIT: <https://github.com/webmapping/nz/commit/c2f09c8aa55ca9c894ae565e0231ae08f6c40ef5>

    - TIPP: die Links zu den Fragen zeigen, wie man direkt auf Codezeilen bei github.com verweisen kann - für die Dokumentation eurer Projekte kann das später hilfreich sein

    - TIPP: mit der rechten Maustaste (oder länger drücken unter MAC) kann man Elemente der Seite im Browser "Untersuchen" und dabei unter anderem nicht nur den HTML Quellcode sondern auch die angewandten CSS-Regeln ein-/ausschalten bzw. verändern - das hilft beim Testen, was die einzelnen Attribute bewirken ...

- Fragen und Antworten

    1. was bewirkt die CSS Regel in Zeile [5-10](https://github.com/webmapping/nz/blob/main/main.css#L5-L10)?

        ```css
        body {
            font-family: 'Open Sans', sans-serif;
            background-color: #ffffff;
            max-width: 1280px;
            margin: auto;
        }
        ```

        - als font-family für die Seite wird "Open Sans" verwendet, ist dieser Font nicht verfügbar kommt eine serifenlose Standardschrift zum Einsatz; die Hintergrundfarbe (background-color) der Seite soll Weiß (#ffffff) sein; die Seite soll nie breiter sein als 1280px (max-width) und hat Links und Rechts den selben Rand (margin: auto) - dadurch wird der Seiteninhalt zentriert

    2. wozu wird das Größer-als-Zeichen (>) in Zeile [12](https://github.com/webmapping/nz/blob/main/main.css#L12) verwendet?

        ```css
        header>img {
            max-width: 100%;
        }
        ```

       - das Größer-als-Zeichen (>) selektiert das erste Bild im Header, also unser Banner und setzt seine Breite auf 100% des Header-Bereichs

    3. was passiert in der CSS Regel in Zeile [16-21](https://github.com/webmapping/nz/blob/main/main.css#L16-L21)?

        ```css
        header a img {
            border-radius: 50%;
            display: block;
            margin: auto;
            margin-top: -50px;
        }
        ```

       - das quadratische User-Bild wird mit border-radius als Kreis dargestellt, über display: block und margin: auto zentriert und mit margin-top: -50px nach oben in das Bannerbild geschoben. Ganz sauber ist das nicht, aber es sieht ganz nett aus ;-) Firefox hat z.B. Probleme beim Darstellen des Links, Edge funktioniert reibungslos

    4. was passiert in der CSS Regel in Zeile [23-30](https://github.com/webmapping/nz/blob/main/main.css#L23-L30)?

        ```css
        main {
            max-width: 1024px;
            margin: auto;
            margin-top: -50px;
            border-left: 1px dashed gray;
            border-right: 1px dashed gray;
            border-bottom: 1px dashed gray;
        }
        ```

       - der Hauptbereich wird mit max-width auf die maximale Brei 1024px gesetzt, über margin: auto wieder zentriert und mit margin-top: -50px um 50px nach oben geschoben - damit ist der Abstand zum User-Bild nicht so groß. Rechts, Links und Unten wird mit border-\[left|right|bottom] ein gestrichelter Rand in grau mit 1px Breite gezeichnet (1px dashed gray)

    5. was bewirkt "padding" in Zeile [33](https://github.com/webmapping/nz/blob/main/main.css#L33)?

        ```css
        article {
            padding: 1em;
        }
        ```

       - mit padding wird der gesamte Inhalt des Artikels um 1em vom Rand nach Innen verschoben - damit "klebt" die Schrift nicht mehr am gestrichelten Rand. Die Einheit em bezieht sich dabei die Schriftgröße und entspricht der Standard font-size 16px. Historisch kommt die Bezeichnung em aus dem Schriftsatz - mehr dazu hier: <https://de.wikipedia.org/wiki/Em_(Schriftsatz)>

    6. wo werden die Fonts *Roboto* und *Montserrat* in Zeile [37](https://github.com/webmapping/nz/blob/main/main.css#L37) & Zeile [45](https://github.com/webmapping/nz/blob/main/main.css#L45) definiert?

       - die beiden Fonts werden in der dritten Zeile bei @import definiert und binden Webfonts ein, die uns Google-Fonts bereitstellt. Mehr dazu bei der nächsten Frage

            ```css
            @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300&family=Open+Sans:ital@0;1&family=Roboto:wght@300&display=swap');
            ```

    7. findet die Quelle aller verwendeten Fonts im Internet

       - die Quelle unserer Fonts ist <https://fonts.google.com/> - dort stehen 100e frei verfügbare Fonts für uns bereit. Einfach Browsen/Suchen, Anklicken und "*Get font*" drücken, dann landet der Font in unserer Auswahl, die wir Rechts/Oben beim Taschensymbol (*Tooltip View selected fonts*) ansehen können. Danach einfach weitersuchen und wenn wir alle gewünschten Fonts gesammelt haben, kann dort unter "*Get embeded Code*" mit der Option "*Web/@import*" die @import CSS Regel kopiert und ins Stylesheet `main.css` übertragen werden.

    8. warum steht eine Raute (#) vor "map" in Zeile [52](https://github.com/webmapping/nz/blob/main/main.css#L52)?

        ```css
        #map {
            width: 90%;
            height: 600px;
            border: 1px solid silver;
            margin: auto;
        }

        ```

       - die Raute (#) ist der sogenannte ID-Selektor - er selektiert HTML-Elemente über deren eindeutige ID - in unserem Fall wird damit das Element mit id="map" angesprochen - unser Kartenfeld, das es in `nz/index.html` noch nicht gibt ;-) Wir werden es später hinzufügen ...

    9. wofür stehen die vier Werte bei padding in Zeile [61](https://github.com/webmapping/nz/blob/main/main.css#L61)?

        ```css
        footer {
            text-align: center;
            padding: 1em 2em 3em 2em;
        }
        ```

       - die vier Werte bestimmen die Abstände des &lt;footer> Elements vom Artikelbereich und Seitenrand in der Reihenfolge Oben -> Rechts -> Unten -> Links (also im Uhrzeigersinn). Würden dort zwei Werte stehen wäre es Oben+Unten -> Rechts+Links

    10. was bewirkt die CSS Regel in Zeile [64-67](https://github.com/webmapping/nz/blob/main/main.css#L64-L67)?

        ```css
        footer nav {
            display: grid;
            grid-template-columns: 40% 20% 40%;
        }
        ```

        - display: grid bewirkt, dass die Navigationslinks im Footer in einem Grid-Layout dargestellt werden. Jedes &lt;a> Element landet in einem eigenen Bereich des Grids, das über grid-template-columns in drei Bereiche mit 40% 20% 40% Breite eingeteilt wird.

    11. welche Elemente werden mit dem Schatten in Zeile [76-78](https://github.com/webmapping/nz/blob/main/main.css#L76-L78) angesprochen?

        ```css
        header img,
        figure>img,
        #map {
            box-shadow:
                0 4px 8px 0 rgba(0, 0, 0, 0.2),
                0 6px 20px 0 rgba(0, 0, 0, 0.2);
        }
        ```

        - alle Bilder im Header (header img) (also Banner und User-Bild), jedes Bild eines &lt;figure> Elements und die Kartenfläche

    12. was bewirkt die @media Regel in Zeile [81-90](https://github.com/webmapping/nz/blob/main/main.css#L81-L90)? Könnt ihr sie im Browser reproduzieren?

        ```css
        @media screen and (max-width: 1280px) {
            main {
                border: none;
            }

            figure {
                margin-left: 0;
                width: 100%;
            }
        }
        ```

        - die @media Regel bewirkt, dass der gestrichelte Rahmen des Artikels und die automatische Einrückung des Bilds innerhalb des &lt;figure> Elements entfernt wird, sobald die Seitenbreite im Browser unter 1280px fällt. Ausprobieren könnt ihr das durch Verändern der Größe des Browserfensters oder die Developer Tools des Browser - im Firefox z.B. F12 und dann Rechts/Oben das Icon für "*Bildschirmgrößen testen*" oder STRG+UMSCHALT+M oder im Menü "*Extras / Browser-Werkzeuge / Bildschirmgrößen testen*" wählen

## Font Awesome

- Font Awesome (<https://fontawesome.com/>) ist eine Icon-Bibliothek mit Hunderten von Webicons die wir in unseren Applikationen verwenden können. Um sie zu verwenden, müssen wir das Stylesheet der Bibliothek verlinken und können dann über Klassenattribute bei &lt;i>-Elementen die einzelnen Icons einbauen.

- der Einbau des Stylesheets im &lt;head>-Bereich von `index.html` erfolgt über ein sogenanntes CDN (Content delivery network). Es gibt viele solche CDNs, wir verwenden <https://cdnjs.com>

    - die Suche dort nach *font-awesome* bringt uns zu <https://cdnjs.com/libraries/font-awesome>

    - wir kopieren den Link-Tag (das mittlere der beiden Icons) von all.min.css und bauen ihn im &lt;head>-Bereich von `index.html` ein - was bedeuten die einzelnen Attribute?

        - integrity: zur Überprüfung, ob beim Laden alles funktioniert hat (i.e. die Datei vollständig)
        - crossorigin: keine Cookies (oder andere Authentifizierung) wird gesendet
        - referrerpolicy: keine Informationen über den Ursprung der Anfrage werden gesendet

      COMMIT: <https://github.com/webmapping/nz/commit/69b5c22f2c4671a7ad15074bcc7bfbbce8874517>

- die Icons finden wir unter <https://fontawesome.com/icons>

    - die Suche nach *camera* bringt uns ein *camera-retro*-Icon, dessen Code zum Einbauen wir direkt kopieren und an der passenden Stelle der HTML-Seite einsetzen können. Dasselbe machen wir mit Pfeilen für die Navigation und einem Home-Symbol für die eigene Etappe

    - ACHTUNG: nur die freien Icons verwenden (über den Blitz bei der Suche filtern) - die PRO-Icons werden nicht angezeigt

    - COMMIT: <https://github.com/webmapping/nz/commit/971ea6d1de8a700925c7bf4bb84ce8cee0c5c10b>

## Leaflet Karte für die Neuseelandreise

Der Ausflug zu CSS ist vorläufig beendet, wir widmen uns jetzt der Karte, die im Neuseelandreise Beispiel noch fehlt

1. zuerst erstellen wir eine leer `main.js` Datei
  
    - COMMIT: <https://github.com/webmapping/nz/commit/6c0cb90c10557785bdec82f57509bd9904b25460>

2. wir binden `main.js` und die Leaflet Bibliothek im &lt;head> Bereich von `index.html` ein - den Code für Leaflet kopieren wir vom top-Beispiel

    - Achtung: wir benötigen sowohl leaflet.css als auch leaflet.js
  
    - COMMIT: <https://github.com/webmapping/nz/commit/9bebcb2d3c8812aab16809d127b5d28061d662dd>

3. wir erstellen den Karten-DIV mit einer Überschrift - die Kartenfläche wird unmittelbar danach angezeigt ...
  
    - COMMIT: <https://github.com/webmapping/nz/commit/6ff8bfa68d11476d2ed3cbc1abf90dc37ba60913>

4. wir kopieren den Javascript Code des top-Beispiels nach `main.js`

    - ACHTUNG: nur den Code zwischen &lt;script> und &lt;/script> kopieren, &lt;script> selber nicht, denn das ist HTML-Syntax und würde im Javascript sofort zu einer Fehlermeldung führen

    - COMMIT: <https://github.com/webmapping/nz/commit/b705ebe6fba614c54a71ac4c3c061d8876314b39>

5. **HMMM**: leider wird die Karte nicht angezeigt :-( Warum?

    - unser Script `main.js` wird im &lt;head> Bereich eingebaut und der Browser führt das Skript aus, sobald er es findet. Leider ist zu diesem Zeitpunkt aber der DIV für die Karte noch nicht "bekannt" und deshalb bekommen wir eine Fehlermeldung im Browser: mit F12 können wir im Tab "Konsole" den Fehler sehen: Uncaught Error: Map container not found und noch einige Details dazu.

    - **!!!WICHTIG**: die Fehlerkonsole ist die erste und wohl wichtigste Anlaufstelle beim Suchen von Fehlern wenn wir mit Javascript/Leaflet programmieren - schaut immer dort hinein, denn oft steht die Lösung des Problems direkt dort.

    - in unserem Fall erfahren wir, dass der Map container nicht gefunden wird und mit eine Zusatzattribut beim &lt;script> können wir das beheben: "**defer**" ist das Zauberwort und bewirkt, dass die Ausführung des Skripts auf den Zeitpunkt "verschoben" wird, an dem der gesamte Inhalt der Seite vom Browser gelesen wurde. Damit ist dann auch der DIV der Karte verfügbar und die Karte kann gezeichnet werden

    ```html
    <script defer src="main.js"></script>
    ```

    - COMMIT: <https://github.com/webmapping/nz/commit/7b7708ceee6039d6233791e924b717d6e9fee4a1>

6. bleibt noch die Koordinaten der Etappe mit Hilfe der Wikipedia-Seite zu korrigieren

    - COMMIT: <https://github.com/webmapping/nz/commit/7cc250080938b5b827bafbcd78a8897cbe481488>

7. die Karte ist damit fertig und eine Fehlermeldung steht noch in der Konsole - scheinbar fehlt da noch ein favicon.ico - angezeigt wird dieses Icon im Browser beim Tab - wir verwenden unser User-Bild dafür und bauen es im &lt;head> Bereich als link rel="shortcut icon" ein

    ```html
    <link rel="shortcut icon" href="images/user.jpg" type="image/x-icon">
    ```

    - COMMIT: <https://github.com/webmapping/nz/commit/123f51cb8286fc12d2a83f155bf766cdb2208a95>

**das war's für heute ...**
