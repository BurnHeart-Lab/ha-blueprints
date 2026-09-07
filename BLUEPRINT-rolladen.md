# Blueprint: Rollläden – Sonne, Wetter, Tag & Nacht

Datei: [`automation/rolladen_sonnenstand_wetter.yaml`](automation/rolladen_sonnenstand_wetter.yaml)

Ein Blueprint pro Himmelsrichtung steuert den kompletten Tagesablauf einer
Rollladen-Gruppe. Alle Teilfunktionen sind integriert, damit sie sich nicht
gegenseitig überfahren.

## Prioritäten

Bei jeder Auswertung gewinnt die höchste zutreffende Stufe:

1. **Unwetter** – Hagel / Gewitter / Sturm jetzt oder in der Stundenvorhersage
   → Position „Unwetter".
2. **Nacht** – zwischen Schließ- und Öffnungszeit → Position „Nacht".
3. **Verschattung** – Sonne scheint (entprellt) auf die Fassade → Position
   „Verschattung".
4. **Offen** – sonst → Position „offen".

## Tagesablauf

| Phase | Auslöser | Verhalten |
|---|---|---|
| **Guten Morgen** | Sonnenaufgang ± Offset, begrenzt auf `[frühestens, spätestens]` | Öffnet. Ist die Sonne dann schon hoch genug + warm + auf der Fassade, wird nur auf die Verschattungs-Position geöffnet und die Tag-Verschattung übernimmt. |
| **Aufsteh-Button** | `input_button`-Druck | Öffnet sofort, unabhängig von der Uhrzeit. Abends gedrückt fahren die Rollläden kurz hoch und die Nacht-Logik schließt wieder. |
| **Verschatten** | Sonne ununterbrochen für „Verzögerung Schließen" auf der Fassade (Azimut ± Toleranz, Höhe ≥ min, Bewölkung ≤ max, Temperatur ≥ Schwelle) | Fährt auf Verschattungs-Position. |
| **Aufhellen** | Bedingung ununterbrochen für „Verzögerung Öffnen" nicht mehr erfüllt | Fährt auf „offen". Längere Verzögerung → kein Jo-Jo bei Wolkenlücken. |
| **Gute Nacht** | Sonnenuntergang ± Offset, begrenzt auf `[frühestens, spätestens]` | Schließt auf „Nacht". „Spätestens" = z. B. Kinder-Schlafenszeit, auch wenn die Sonne noch scheint. „Frühestens" = Winterschutz gegen „16:30 schon dunkel". |
| **Unwetter** | Wetterzustand/-vorhersage | Schließt auf „Unwetter" (0 = Scheibenschutz bei Hagel), Vorrang vor allem. |

Für unterschiedliche Schlafenszeiten (Kinderzimmer 19:30, Rest 21:30) einfach
**zwei Instanzen** mit verschiedener „spätester Schließzeit" anlegen.

## Empfehlungswerte

| Eingabe | Empfehlung | Hinweis |
|---|---|---|
| Position „offen" | `100` | |
| Position „Verschattung" (zu %) | `30` | Lüftungsspalt gegen Wärmestau; `0` nur für Verdunkelung |
| Position „Nacht" | `0` | `10–20` für Nachtluft im Sommer |
| Position „Unwetter" | `0` | schützt die Scheibe bei Hagel |
| Fassaden-Azimut | reale Wandrichtung | 0 N · 90 O · 180 S · 225 SW · 270 W |
| Azimut-Toleranz ± | `70°` | `65–75°`. `90°` verschattet bis die Sonne fast hinterm Haus steht (streifender Einfall zählt dann noch) |
| Minimale Sonnenhöhe | `10°` | Ost/West `3–5°` (tiefe Sonne scheint dort am tiefsten rein) |
| Maximale Sonnenhöhe | `0` (aus) | nur bei Dachüberstand über Mittag |
| Max. Bewölkung | `40 %` | `30–50 %` |
| Verschatten erst ab | `18 °C` | `0` = Temperatur ignorieren; hält die Verschattung an kühlen Sonnentagen aus |
| Verzögerung Schließen | `15 min` | `10–20` |
| Verzögerung Öffnen | `30 min` | `20–45` |
| Offset Sonnenaufgang | `0` … `+15 min` | |
| Frühestens öffnen | `06:30` | eigene Instanz fürs Wochenende, falls später |
| Spätestens öffnen | `09:00` | Wintertage mit spätem Sonnenaufgang |
| Offset Sonnenuntergang | `−15 min` … `0` | |
| Frühestens schließen | `17:00` | Winterschutz |
| Spätestens schließen | `21:30` | Kinderzimmer `19:30`; `23:59` = keine Obergrenze |
| Schutz-Wetterzustände | `hail, lightning-rainy, snowy-rainy` | optional `lightning`, `pouring` |
| Wind-Schwelle (Dauerwind) | `55 km/h` (Bft 8) | `45–70` Rollläden · `25–35` Markisen; Eingabe in km/h, Umrechnung automatisch |
| Böen-Schwelle | `70 km/h` | `0` = aus; nutzt `wind_gust_speed`, falls die Wetter-Entität es liefert (WetterOnline ja, met.no nein) |
| Vorhersage-Vorlauf | `2 h` | `1–3 h`; `0` = nur aktuelle Lage |
| Positions-Toleranz | `5 %` | Handbetrieb-Erkennung |

## Installation

1. `input_button`-Helfer für „Aufstehen" anlegen (Einstellungen → Geräte &
   Dienste → Helfer → Taste).
2. Blueprint importieren (URL siehe [README](README.md)).
3. **Automation erstellen → Aus Blueprint** → Formular ausfüllen.
4. Pro Himmelsrichtung wiederholen.

## Handbetrieb-Erkennung

Die Automation fährt einen Rollladen nur, wenn er noch nahe an der zuletzt von
ihr gesetzten Position steht (± Positions-Toleranz). Hast du ihn von Hand
deutlich woanders hingestellt, bleibt er in Ruhe – bis zur nächsten Phase, die
ihn ohnehin bewegen würde (z. B. Nacht).

## Bekannte Randfälle

- **Verschattungs-Position von Nacht/Unwetter unterscheiden** (z. B. 30 vs 0).
  Sind sie gleich, kann eine kurze Wolke die „Verzögerung Öffnen" umgehen.
- **Neustart:** Home Assistant stellt beim Start den zur Uhrzeit passenden
  Zustand ohne Verzögerung her.
- **Unwetter bei Hitze:** Zieht ein Gewitter an einem heißen, sonnigen
  Nachmittag durch, bleiben die Rollläden nach dem Unwetter auf
  „Unwetter"-Position (dunkel, aber kühl), bis die nächste Verschattungs-Kante,
  ein Neustart oder der Abend kommt.
- **Wetter-Entität-Ausfall:** Geht sie auf `unavailable`/`unknown`, wird
  Unwetterschutz + Verschattung übersprungen (Nacht/Morgen laufen weiter). Der
  Forecast-Aufruf bricht die Automation nicht mehr ab. met.no lässt sich im
  Formular als manueller Ersatz eintragen.
- **`wind_gust_speed`** fehlt bei manchen Integrationen (z. B. met.no) – dort ist
  die Böen-Schwelle wirkungslos, der Dauerwind zählt weiter.
- Existiert bereits eine eigene Sonnenuntergangs-/Morgen-Automation für dieselben
  Rollläden, sollte sie deaktiviert werden – dieses Blueprint übernimmt das.
