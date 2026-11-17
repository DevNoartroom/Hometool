# Sicherheit – Hometool

## Unterstützte Versionen

| Version | Sicherheits-Updates |
|---|---|
| 1.x | ✅ ja |

Es wird immer nur die aktuelle Version im `main`-Branch gepflegt.

---

## Sicherheitslücke melden

Bitte **kein öffentliches Issue** für Sicherheitsprobleme anlegen.

1. Im Repository auf den Reiter **Security** gehen.
2. **Report a vulnerability** wählen und das Problem beschreiben:
   - was passiert,
   - wie man es nachstellt (Schritte, ggf. eine Beispiel-JSON-Datei),
   - welcher Browser und welches Gerät.

Hometool ist ein privates Hobbyprojekt. Meldungen werden nach Möglichkeit zeitnah gelesen und beantwortet, eine feste Reaktionszeit gibt es nicht. Wer eine Lücke verantwortungsvoll meldet, wird auf Wunsch im Änderungsprotokoll genannt.

---

## Sicherheitsmodell

Hometool ist eine **einzelne HTML-Datei ohne Server**. Daraus ergibt sich:

| Thema | Umsetzung |
|---|---|
| **Datenspeicherung** | Nur im `localStorage` des Browsers (Schlüssel `home-v1`). Kein Server, kein Konto, keine Cloud |
| **Netzwerk** | Die App stellt keine Netzwerkanfragen. Eine Content-Security-Policy im `<head>` verbietet externe Skripte, Styles, Bilder, Schriften und Verbindungen (`default-src 'none'`, `connect-src 'none'`) |
| **Tracking** | Keins. Keine Analytics, keine Cookies, keine Drittanbieter |
| **Ausgabe von Text** | Alle Nutzereingaben und importierten Inhalte werden vor der Anzeige HTML-maskiert (`esc()`), damit eingeschleuster HTML- oder Skript-Code nicht ausgeführt wird |
| **Import (JSON)** | Max. 5 MB. Jedes Feld wird auf Typ, Länge und erlaubte Werte geprüft. Steuerzeichen werden entfernt, IDs dürfen nur `A–Z a–z 0–9 _ -` enthalten. Unbekannte Felder werden verworfen, ungültige Dateien ändern nichts |
| **Kalender-Export (.ics)** | Texte werden nach RFC 5545 maskiert, UIDs zusätzlich gefiltert, damit keine Zeilen eingeschleust werden können |
| **Speicherfehler** | Schlägt das Speichern fehl (z. B. privater Modus, voller Speicher), zeigt die App einmal pro Sitzung eine Warnung |

---

## Bekannte Grenzen – bitte beachten

1. **Die Daten sind nicht verschlüsselt.** Wer Zugriff auf dein entsperrtes Gerät oder deinen Browser hat, kann sie lesen.
2. **Keine sensiblen Daten speichern.** Keine PINs, Passwörter, Konto- oder Kartennummern in Aufgaben, Notizen oder Vorräte eintragen. „Bank checken“ ist als Erinnerung gedacht, nicht als Ablage für Bankdaten.
3. **Gleiche Domain, gleicher Speicher.** Der Browser-Speicher gilt pro Domain. Unter GitHub Pages teilen sich *alle* Projekte unter `DEINNAME.github.io` dieselbe Domain. Ein anderes Projekt dort könnte die Hometool-Daten technisch auslesen. Wer das ausschließen will, nutzt eine eigene (Sub-)Domain für Hometool oder hostet dort keine fremden Skripte.
4. **Exporte sind Klartext.** JSON- und `.ics`-Dateien enthalten alle Einträge lesbar. Verschicke sie nur an Personen, denen du die Inhalte zeigen willst.
5. **Nur vertrauenswürdige Dateien importieren.** Der Import ist abgesichert, übernimmt aber Inhalte (Aufgaben, Notizen) in deine Liste. Importiere nur Dateien von Personen, die du kennst.
6. **Datenverlust.** Browserdaten löschen, ein neues Gerät oder ein Browser im privaten Modus bedeuten leere Daten. Regelmäßig über „Daten exportieren“ sichern.

---

## Für Mitwirkende

- Keine externen Bibliotheken, CDNs oder Schriftarten einbinden (verstößt gegen die Content-Security-Policy und gegen das Datenschutz-Versprechen).
- Neue Felder im Datenformat immer in `cleanImport()` prüfen und begrenzen.
- Jede Ausgabe von Nutzertext über `esc()` führen, nie direkt per `innerHTML` einsetzen.
