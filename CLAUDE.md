# Brettspiel-Randos

Sammlung von Randomizern, die beim **Spielaufbau von Brettspielen** helfen (z. B. zufällige Kartenauswahl, Startspieler, Szenario-/Modul-Auswahl, Fraktionen, Spielplan-Layouts).
Die Seiten werden als statisches HTML über **GitHub Pages** gehostet und hauptsächlich **am Handy** am Spieltisch benutzt.

## Grundprinzipien

- **Rein statisch**: nur HTML, CSS und Vanilla-JavaScript. Kein Build-Schritt, kein Framework, kein Backend, kein npm.
- **Eine Datei pro Randomizer**: jeder Randomizer ist eine eigenständige `.html`-Datei mit inline `<style>` und `<script>`, damit sie auch einzeln funktioniert.
- **Keine externen Abhängigkeiten**, wenn vermeidbar (keine CDNs, keine Webfonts). Die Seiten sollen auch mit schlechtem Empfang bzw. offline nach dem ersten Laden funktionieren.
- **Zweisprachig: Deutsch und Englisch, umschaltbar** (siehe Abschnitt „Mehrsprachigkeit“). Standardsprache ist Deutsch.

## Ordnerstruktur

```
/
├── index.html            # Startseite: Liste/Kacheln aller Randomizer
├── CLAUDE.md
├── README.md
├── assets/
│   └── shared.css        # optional: gemeinsame Basis-Styles (Farben, Buttons)
└── spiele/
    ├── <spielname>.html  # ein Randomizer pro Spiel, Dateiname kebab-case, ohne Umlaute
    └── ...
```

- Spielregeln (PDF, DE + EN) liegen in einem Ordner pro Spiel (z. B. `Riffwelten/`). Sie sind die Quelle für Begriffe und Aufbauregeln – Wording in beiden Sprachen genau aus den Regeln übernehmen. PDFs werden **nicht** veröffentlicht (`.gitignore`).
- Neue Randomizer **immer** auf der `index.html` verlinken (Array `GAMES`).
- Dateinamen: Kleinbuchstaben, Bindestriche, Umlaute ersetzen (`ä→ae`, `ö→oe`, `ü→ue`, `ß→ss`), z. B. `spiele/die-siedler-von-catan.html`.
- Relative Pfade verwenden (GitHub Pages läuft unter `/<repo-name>/`, nicht unter `/`).

## Mobile-First-Anforderungen (Pflicht)

Jede Seite muss primär für Smartphones im Hochformat (ab 360 px Breite) gebaut sein:

- `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">` im `<head>`.
- **Mobile-first CSS**: Basis-Styles für schmale Bildschirme, größere Layouts nur per `@media (min-width: …)`.
- **Kein horizontales Scrollen**; Seitenrand mind. 16 px; `env(safe-area-inset-*)` für Notch/Home-Indicator berücksichtigen.
- **Große Touch-Ziele**: Buttons/Checkboxen mind. 44×44 px, ausreichend Abstand zwischen klickbaren Elementen.
- **Lesbare Schrift**: Basis mind. 16 px (verhindert auch Auto-Zoom bei Eingabefeldern in iOS); Systemschrift (`system-ui, sans-serif`).
- **Hauptaktion gut erreichbar**: der „Würfeln/Neu mischen“-Button liegt im Daumenbereich (unten, ggf. `position: sticky`) und ist groß und eindeutig.
- **Kein Hover-abhängiges Verhalten** – alles muss per Tap bedienbar sein.
- **Dark Mode** über `@media (prefers-color-scheme: dark)` mit CSS-Variablen auf `:root` unterstützen (Spieleabende sind oft abends/gedimmt).
- Ergebnisse groß und übersichtlich darstellen, damit sie über den Tisch hinweg ablesbar sind.
- Optional: Bildschirm wach halten über die Wake Lock API (`navigator.wakeLock`), mit Fallback, falls nicht unterstützt.

## Mehrsprachigkeit (DE / EN)

Jede Seite (inkl. `index.html`) muss zwischen **Deutsch** und **Englisch** umschaltbar sein.

- **Umschalter**: gut sichtbarer, tappbarer Toggle oben rechts (z. B. `DE | EN`), mind. 44×44 px Touch-Fläche, aktive Sprache hervorgehoben.
- **Startsprache**: 1. gespeicherte Wahl aus `localStorage` (Schlüssel `lang`, gemeinsam für alle Seiten), 2. sonst Browsersprache (`navigator.language` beginnt mit `de` → Deutsch, sonst Englisch), 3. Fallback Deutsch.
- **Umschalten ohne Neuladen**: alle Texte werden sofort neu gerendert; bereits gewürfelte Ergebnisse bleiben erhalten und werden nur übersetzt.
- `<html lang="…">` und `<title>` beim Umschalten mit aktualisieren.
- **UI-Texte** in einem Objekt `I18N = { de: {…}, en: {…} }` im Script ablegen, statische Elemente per `data-i18n="schluessel"` markieren und über eine Funktion `t(key)` befüllen. Keine hart codierten UI-Texte im HTML/JS.
- Beide Sprachen müssen **immer vollständig** sein – jeder Schlüssel existiert in `de` und `en`. Fehlt ein Schlüssel, auf Deutsch zurückfallen und in der Konsole warnen.
- **Spieldaten**: Namen von Karten, Fraktionen, Modulen etc. bekommen beide offiziellen Bezeichnungen, z. B. `{ id: "harbor", de: "Hafen", en: "Harbor" }`. Logik arbeitet nur mit der sprachunabhängigen `id` (auch für gespeicherte Einstellungen in `localStorage`). Gibt es keine offizielle Übersetzung, in beiden Sprachen den Originalnamen verwenden.
- Mengenangaben/Plurale sauber übersetzen (z. B. „1 Spieler / 2 Spieler“ vs. „1 player / 2 players“), nicht einfach Strings zusammensetzen.

## Funktionale Konventionen

- **Zufall**: `crypto.getRandomValues` bzw. ein sauberer **Fisher-Yates-Shuffle** – kein `array.sort(() => Math.random() - 0.5)`.
- **Spieldaten** (Kartenlisten, Erweiterungen etc.) als JS-Objekt/Array am Anfang des Scripts definieren, getrennt von der Logik, damit sie leicht ergänzt werden können.
- **Einstellungen** (z. B. Spielerzahl, aktive Erweiterungen, ausgeschlossene Karten) in `localStorage` speichern – Zugriffe immer in `try/catch`, die Seite muss auch ohne Storage funktionieren.
- Regeln/Einschränkungen des Spiels beim Randomisieren beachten (z. B. Mindestanzahl pro Kategorie, Abhängigkeiten zwischen Erweiterungen) und im Code kommentieren.
- Ein **„Zurück zur Übersicht“**-Link auf jeder Randomizer-Seite.
- Keine urheberrechtlich geschützten Bilder/Artworks einbinden – nur Text (Namen) bzw. selbst erstellte Symbole/Emojis.

## Seitengerüst für neue Randomizer

```html
<!doctype html>
<html lang="de">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
  <meta name="theme-color" content="#1e1e2e">
  <title>Spielname – Randomizer</title>
  <style>
    :root { --bg:#ffffff; --fg:#1a1a1a; --accent:#2f6fde; --card:#f2f2f5; }
    @media (prefers-color-scheme: dark) {
      :root { --bg:#16161d; --fg:#ececf1; --accent:#6b9cff; --card:#23232d; }
    }
    * { box-sizing: border-box; }
    body { margin:0; padding:16px; padding-bottom:calc(96px + env(safe-area-inset-bottom));
           font:16px/1.5 system-ui, sans-serif; background:var(--bg); color:var(--fg); }
    .roll { position:sticky; bottom:calc(16px + env(safe-area-inset-bottom)); width:100%;
            min-height:56px; font-size:1.2rem; border:0; border-radius:12px;
            background:var(--accent); color:#fff; }
  </style>
</head>
<body>
  <header>
    <a href="../index.html" data-i18n="back">← Übersicht</a>
    <div class="lang-toggle">
      <button type="button" data-lang="de">DE</button>
      <button type="button" data-lang="en">EN</button>
    </div>
  </header>
  <h1 data-i18n="title">Spielname</h1>
  <!-- Einstellungen -->
  <!-- Ergebnis -->
  <button class="roll" type="button" data-i18n="roll">Neu würfeln</button>
  <script>
    // 1. Übersetzungen
    const I18N = {
      de: { back: "← Übersicht", title: "Spielname", roll: "Neu würfeln" },
      en: { back: "← Overview",  title: "Game name", roll: "Reroll" },
    };
    // 2. Spieldaten (mit id + de/en-Namen)
    // 3. Sprache ermitteln/umschalten (localStorage "lang", navigator.language, Fallback de)
    // 4. Zufallsfunktionen (Fisher-Yates mit crypto.getRandomValues)
    // 5. Einstellungen laden/speichern (localStorage, try/catch)
    // 6. Rendern (alle Texte über t(key))
  </script>
</body>
</html>
```

## Testen

- Lokal einfach die HTML-Datei im Browser öffnen oder einen simplen Server starten (`python -m http.server`).
- Immer in der mobilen Ansicht prüfen (DevTools Geräte-Emulation, z. B. 375×812) – inkl. Dark Mode.
- Prüfen: kein horizontales Scrollen, alle Buttons gut tappbar, Ergebnis nach dem Würfeln sofort sichtbar.
- In **beiden Sprachen** prüfen: alle Texte übersetzt, längere englische/deutsche Texte brechen nicht das Layout, Sprachwahl bleibt beim Seitenwechsel erhalten.

## Deployment (GitHub Pages)

- Repository auf GitHub, Pages in den Repo-Einstellungen auf Branch `main`, Ordner `/ (root)` stellen.
- URL: `https://<github-user>.github.io/<repo-name>/`
- Nur committen/pushen, wenn ausdrücklich gewünscht.
