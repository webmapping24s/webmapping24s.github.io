# Session 1 - HTML Erstkontakt (7.3.2024)

## GIT Repo erstellen und clonen

- Account erstellen bei <https://github.com>

- Repo `nz` erstellen - <https://github.com/webmapping/nz>

- beim Pulldown "*&lt;> Code*" die HTTPS Adresse kopieren - <https://github.com/webmapping/nz.git>

- Visual Studio Code starten

- über "*F1 / Git: Clone*" die HTTPS Adresse einkopieren und damit das Repo `nz` clonen

- Speicherort wählen und Repo als Folder öffnen

- neue `index.html` Datei im "*Explorer Bereich*" (oberstes Icon in der linken Symbolleiste) erstellen

    - *hmtl 5* Baustein einfügen

        - "*htm*" schreiben und dann das Template `html:5` auswählen - Titel eintragen und speichern

        - im "*Source Control Bereich*" (drittes Icon in der linken Symbolleiste) mit dem Plus rechts von `index.html` "*Stage Changes*" und mit einer sprechenden Commit-Message "*Commit*" drücken

        - COMMIT: <https://github.com/webmapping/nz/commit/c9de9780a824481e21c8b1c5eea311693a64a1f6>

        - **Achtung**: wenn E-Mail/Name noch nicht bekannt sind, kommt jetzt eine Fehlermeldung

            - "*Terminal / New Terminal*" aus dem Menü wählen und diese zwei Zeilen mit den eigenen Angaben dorthin kopieren und mit ENTER bestätigen

                ```shell
                git config --global user.email "email@example.com"
                git config --global user.name "username"
                ```

    - Titel ändern

        ```html
        <h1>Neuseelandreise</h1>
        ```

        - COMMIT: <https://github.com/webmapping/nz/commit/d1840c9f38fa571f6196d21d8b82b2d21244cba5>

    - Sprache ändern

        ```html
        <html lang="de">
        ```

        - COMMIT: <https://github.com/webmapping/nz/commit/f85a08a9bb5ede86692cc511745f32418b030163>

- Synchronisieren der Änderungen zurück nach <https://github.com/webmapping/nz>

    - im "*Source Control Bereich*" Rechts / Oben auf die drei Punkte klicken und **Push** auswählen - den Anordnungen folgen

    - damit sind die Änderungen bei <https://github.com/webmapping/nz> sichtbar

- Repo online stellen

    - im Menü des Repos auf github.com "*Settings*" wählen

    - Links "*Pages*" wählen

    - unter "*Build and deployment*" bei "Branch" "*main*" wählen und "*Save*"

    - damit ist nach kurzer Wartezeit das Repo unter <https://webmapping.github.io/nz> im Web erreichbar

- Vscode schließen und noch einmal alles herholen

    - "*File / Open Folder*" wählen, dann ist das Repo richtig geöffnet ("*File / Close Folder*" zum Schließen)

    - die History unserer Commit Nachrichten landet in der Timeline im linken Tab

    - auch Änderungen zur letzten Version sind im "*Source Control Bereich*" zu finden

    - Code formatieren wir mit "*F1 / Format*" (kann auch automatisch eingestellt werden ...)
  
    - Lokal Online gehen können wir mit einem Plugin

        - im "*Extensions Bereich*" (letztes Icon in der linken Symbolleiste) nach *liveserver* suchen und installieren

        - Rechts / Unten beim Symbol "*Go Live*" können wir unsere Webseite lokal im Browser öffnen. Alle Änderungen werden direkt nach dem Speichern angezeigt

## HTML webseite weiter entwickeln

### Untertitel und Absätze mit formatiertem Text hinzufügen

```html
<h2><em>Tongariro-Nationalpark</em></h2>
```

- Tipp: VS Code zeigt Tooltips bei Elementen & Attributen. Links zu den MDN Web Docs (<https://developer.mozilla.org/>) sind sehr hilfreich

- Wikipedia-Suche "Tongariro-Nationalpark" bringt <https://de.wikipedia.org/wiki/Tongariro-Nationalpark>

- die ersten drei Absätze kopieren

    ```html
    <p>Der Tongariro-Nationalpark  ... Vulkane als heilig verehren.</p>
    ```

    COMMIT: <https://github.com/webmapping/nz/commit/31032149765b617701b2fe0aa2176d3405608992>

- fett / kursiv formatieren

    ```html
    <strong>Tongariro-Nationalpark</strong>
    <em>älteste Nationalpark Neuseelands</em>
    ```

    auch verschachtelt möglich, aber auf Reihenfolge beim Öffnen / Schließen der Tags achten!

    ```html
    <strong><em>Weltnaturerbe der UNESCO</em></strong> (richtig)
    <strong><em>Weltnaturerbe der UNESCO</strong></em> (falsch)
    ```

    COMMIT: <https://github.com/webmapping/nz/commit/abf843088123407ba02e350b0d8e1c60377bb0ac>

- Link zur Wikipedia Quelle mit &lt;a> wie *anchor* und href-Attribut wie *hyperlink reference*, der Linktext steht dazwischen

    ```html
    <a href="https://de.wikipedia.org/wiki/Tongariro-Nationalpark">Quelle Wikipedia</a>
    ```

    COMMIT: <https://github.com/webmapping/nz/commit/ff3b7fe4c224a71034fdc2092d39ace422118964>

### Bild der Emerald Lakes hinzufügen

- wir brauchen ein frei verfügbares Bild von <https://pixabay.com> oder <https://commons.wikimedia.org>

    - Suche bei Pixabay nach "Emerald Lakes" bringt <https://pixabay.com/photos/emerald-lakes-3189389/>

    - wir speichern direkt das Vorschaubild

    - Zielort: `images/attraction.jpg`

    COMMIT: <https://github.com/webmapping/nz/commit/a47b14081b02366c5d8e96e10bc9c2c9396d6f7f>

- Bild einbauen als &lt;figure> (Abbildung)

    - Bild als &lt;img> mit src-Attribut (source) für Bildquelle und alt-Attribut für alternativen Text, wenn das Bild nicht angezeigt wird - wichtig für Blinde!

    - Bildunterschrift als &lt;figcaption> mit einem &lt;a> Link zur Bildquelle

    ```html
    <figure>
        <img src="images/attraction.jpg" alt="Blick auf drei Vulkanseen im Gipfelbereich des Tongariro">
        <figcaption>Blick auf die Emerald Lakes (Quelle: <a href="https://pixabay.com/photos/new-zealand-tongariro-crossing-83645/">tongariro@pixabay</a>  )</figcaption>
    </figure>
    ```

    - COMMIT: <https://github.com/webmapping/nz/commit/598111c8b1ac76f936e4f8e92c146a99fdb8e021>

### Linkliste mit drei beliebigen Links

- als &lt;ul> (unordered list) mit &lt;li> (list item) - z.B. Weblinks der Wikipedia-Seite verwenden

    ```html
    <ul>
        <li><a href="...">Link 1</a></li>
        <li><a href="...">Link 2</a></li>
        <li><a href="...">Link 3</a></li>
    </ul>
    ```

    - COMMIT: <https://github.com/webmapping/nz/commit/ca837f0f717586678f508fb8bbc38109b8c1ac57>

### Etappennavigation zu den Nachbar:innen

- als &lt;nav> (navigation) mit Links - Nachbar:innen siehe OLAT Ordner `etappen.ods`

```html
<nav>
    <a href="https://laurauniibk.github.io/nz">vorhergehende Etappe</a>
    <a href="https://webmapping.github.io/nz">diese Etappe</a>
    <a href="https://ernstanton.github.io/nz">nächste Etappe</a>
</nav>
```

- COMMIT: <https://github.com/webmapping/nz/commit/912444a217569723c1700dd2276dfca66f4b5cd7>

**das war's für heute ...**
