# Hometool – Betrieb und Technik

Diese Seite ist für alle, die Hometool selbst veröffentlichen, aktualisieren oder weiterentwickeln wollen. Für die Bedienung siehe die [README](../README.md).

---

## Inhalt des Repositorys

| Datei | Zweck |
|---|---|
| `index.html` | Die komplette App: HTML, CSS und JavaScript in einer Datei |
| `README.md` | Startseite des Repositorys für Nutzer |
| `SECURITY.md` | Sicherheitsmodell und Meldeweg für Sicherheitsprobleme |
| `docs/BETRIEB.md` | Diese Seite |
| `docs/screenshot-*.png` | Bilder für die README (mit erfundenen Beispieldaten) |

---

## Auf GitHub Pages veröffentlichen

Danach ist die App unter `https://DEINNAME.github.io/hometool/` erreichbar. Ersetze `DEINNAME` in der README durch deinen GitHub-Namen, damit der Link „App öffnen“ funktioniert.

### Variante A: nur im Browser (ohne Terminal)

1. Bei [github.com](https://github.com) anmelden → oben rechts **+** → **New repository**.
2. Name `hometool`, **Public** wählen → **Create repository**.
3. Auf der leeren Seite **uploading an existing file** anklicken.
4. `index.html`, `README.md`, `SECURITY.md` und den Ordner `docs` ins Fenster ziehen → unten **Commit changes**.
5. Im Repository **Settings** → links **Pages**.
6. Bei **Branch** `main` und `/ (root)` wählen → **Save**.
7. Nach 1–2 Minuten steht oben auf der Seite der Link zur App.

### Variante B: mit dem Terminal

Voraussetzung: [Git](https://git-scm.com) ist installiert und das leere Repository `hometool` existiert auf GitHub (Schritte 1–2 oben).

```bash
git clone https://github.com/DEINNAME/hometool.git   # lädt das (leere) Repository auf deinen Computer
cd hometool                                          # wechselt in den neuen Ordner
# jetzt index.html, README.md, SECURITY.md und docs/ in diesen Ordner kopieren
git add index.html README.md SECURITY.md docs        # merkt diese Dateien für den nächsten Speicherstand vor
git commit -m "Hometool v1"                          # speichert einen Stand mit dieser Beschreibung
git push                                             # lädt den Stand zu GitHub hoch
git tag v1.0                                         # markiert diesen Stand als Version 1.0
git push --tags                                      # lädt die Markierung zu GitHub hoch
```

Danach GitHub Pages wie in Variante A, Schritte 5–7, einschalten.

### Sicherheitsmeldungen einschalten

Damit „Report a vulnerability“ aus der `SECURITY.md` funktioniert:
**Settings** → **Code security** (je nach Ansicht „Security“) → bei **Private vulnerability reporting** auf **Enable** klicken.

---

## Eine neue Version veröffentlichen

1. Die neue `index.html` über die alte kopieren (im Browser: Datei im Repository öffnen → Stift-Symbol bzw. **Add file → Upload files**).
2. Mit dem Terminal:

```bash
git add index.html                    # merkt die geänderte Datei vor
git commit -m "Hometool v1.1"         # speichert den neuen Stand
git push                              # lädt ihn zu GitHub hoch
git tag v1.1                          # markiert ihn als Version 1.1
git push --tags                       # lädt die Markierung hoch
```

Die Daten der Nutzer bleiben bei einem Update erhalten, solange der Speicher-Schlüssel `home-v1` gleich bleibt.

**Zurück zu einer alten Version**, falls ein Update etwas kaputt macht:

```bash
git checkout v1.0 -- index.html       # holt die index.html aus Version 1.0 zurück
git commit -m "Zurück auf v1.0"       # speichert diesen Stand
git push                              # lädt ihn hoch
```

---

## Wo die Daten liegen

- Im `localStorage` des Browsers, Schlüssel `home-v1`.
- Der Speicher gilt **pro Domain**: Alle Projekte unter `DEINNAME.github.io` teilen ihn sich. Andere Apps nutzen in der Regel andere Schlüssel, es gibt also keinen Konflikt, aber ein fremdes Skript auf derselben Domain könnte mitlesen. Siehe `SECURITY.md`.

---

## Datenformat (JSON-Export)

```json
{
  "app": "claudeytools-home",
  "v": 1,
  "tasks":  [{ "id": "t…", "n": "Bad putzen", "p": "Sam", "r": "weekly", "days": [5],
               "start": "2026-11-17", "tm": "11:00", "du": 60, "steps": ["Dusche", "WC"], "note": "" }],
  "shop":   [{ "id": "s…", "n": "Brot", "q": "1", "x": 0 }],
  "food":   [{ "id": "f…", "n": "Milch", "q": "1 l", "exp": "2026-11-20", "o": "Kühlschrank", "b": "2026-11-17" }],
  "notes":  [{ "id": "n…", "t": "Vermieter kommt Do 10 Uhr", "a": "Alex", "d": "2026-11-17T09:12", "c": "#d4efe3", "pin": 1 }],
  "people": ["Alex", "Sam"],
  "done":   { "t…|2026-11-17": 1 }
}
```

| Feld | Bedeutung |
|---|---|
| `tasks[].r` | Wiederholung: `once` (mit `date`, leer = jederzeit), `daily`, `weekly` (`days`: 0 = Montag … 6 = Sonntag), `monthly` (`md`: 1–28, 0 = letzter Tag), `interval` (`iv`: alle X Tage ab `start`) |
| `tasks[].tm` / `du` | Uhrzeit `HH:MM` (optional) und Dauer in Minuten |
| `shop[].x` | 1 = gekauft |
| `food[].o` | `Kühlschrank`, `Gefrierfach` oder `Vorratsschrank`; `b` = Kaufdatum |
| `notes[].c` | Farbe: `#d4efe3`, `#9ddbd2`, `#75c2b9` oder `#7d9c9c` |
| `done` | Schlüssel `Aufgaben-ID\|Datum` (bzw. `\|-` bei „jederzeit“), Teilschritte `…\|Datum\|Nummer` |

Datumsangaben im Format `JJJJ-MM-TT`. Beim Import werden Einträge mit gleicher `id` ersetzt, alle Felder geprüft und Unbekanntes verworfen.

---

## Technik

- Eine Datei, reines HTML, CSS und JavaScript, ohne Frameworks und ohne Build-Schritt.
- Content-Security-Policy im `<head>`: keine externen Ressourcen, keine Netzwerkverbindungen.
- Icons sind eigene SVG-Zeichnungen im Code (`E` und `EK` im Skript). Neue Stichwörter ergänzt man in `EK`.
- Farbpalette: `#d4efe3` `#9ddbd2` `#75c2b9` `#63b7ad` `#76b8a9` `#275951` `#629788` `#7d9c9c` `#4d6c62` `#4e6c6c`

### Regeln für Änderungen

- Keine externen Bibliotheken, CDNs oder Schriften einbinden.
- Jede Ausgabe von Nutzertext über `esc()` führen.
- Neue Datenfelder immer in `cleanImport()` prüfen und begrenzen.
- Den Speicher-Schlüssel `home-v1` nicht umbenennen, sonst sind die Daten der Nutzer scheinbar weg.

---

## Grenzen von Version 1

- Keine Live-Synchronisation zwischen Geräten (bräuchte einen Server, z. B. PocketBase).
- `.ics`-Dateien kennen keinen Erledigt-Status.
- In manchen eingebetteten Vorschauen (z. B. in Chat-Apps) sind Downloads gesperrt. Im normalen Browser und über GitHub Pages funktionieren sie.

---

## Lizenz

Noch keine Lizenz festgelegt. Ohne Lizenz dürfen andere den Code ansehen, aber nicht weiterverwenden. Eine Vorlage (z. B. MIT) bekommst du auf GitHub über **Add file → Create new file**, Dateiname `LICENSE`, dann rechts **Choose a license template**.
