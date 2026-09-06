# Blueprint: Rollläden nach Sonnenstand & Wetter

Datei: [`automation/rolladen_sonnenstand_wetter.yaml`](automation/rolladen_sonnenstand_wetter.yaml)

## Was sie macht

- **Verschatten:** Scheint die Sonne lange genug (Verzögerung Schließen) auf die
  angegebene Fassade – Azimut in Toleranz, Sonne hoch genug, nicht zu bewölkt –
  fahren die Rollläden auf die Verschattungs-Position.
- **Öffnen:** Ist es lange genug (Verzögerung Öffnen) nicht mehr besonnt, fahren
  sie wieder hoch. Die Öffnen-Verzögerung ist bewusst länger → kein Jo-Jo bei
  Wolkenlücken.
- **Unwetterschutz (Vorrang):** Bei Hagel, Gewitter oder Sturm – aktuell **oder**
  in der Stundenvorhersage der nächsten Stunden – fahren die Rollläden zu.
- **Handbetrieb:** Wurde ein Rollladen von Hand deutlich anders gestellt, lässt
  die Automation ihn in Ruhe (Positions-Toleranz).

Pro Himmelsrichtung **eine eigene Automation** aus dem Blueprint anlegen
(Süd, West, Ost …), jeweils mit den Covern dieser Seite.

## Installation

Es gibt keinen HA-REST-Weg, Blueprint-Dateien zu schreiben – daher manuell:

**Variante A – Editor-Add-on (Studio Code Server / File Editor):**
1. Ordner `/config/blueprints/automation/ha_agent/` anlegen.
2. `rolladen_sonnenstand_wetter.yaml` dort hineinlegen (Inhalt aus diesem Repo).
3. HA → Einstellungen → Automationen & Szenen → **Blueprints** → sollte
   „Rollläden nach Sonnenstand & Wetter" zeigen. Sonst: Entwicklerwerkzeuge →
   YAML → *Automationen neu laden*.

**Variante B – Import über URL:** Blueprint als Raw-YAML hosten (z. B. Gist),
dann HA → Blueprints → *Blueprint importieren* → URL einfügen.

Danach: **Automation erstellen → Aus Blueprint → Rollläden nach Sonnenstand & Wetter.**

## Empfehlungswerte

| Eingabe | Empfehlung | Bereich / Hinweis |
|---|---|---|
| Position beim Verschatten ("zu %") | `30` | Lüftungsspalt gegen Wärmestau; `0` nur für Verdunkelung, `40–50` für helle Räume |
| Position beim Öffnen ("auf %") | `100` | |
| Fassaden-Azimut | reale Wandrichtung | 0 N · 90 O · 180 S · 225 SW · 270 W |
| Azimut-Toleranz ± | `90°` | `70–80°` für nur direkte Einstrahlung |
| Minimale Sonnenhöhe | `10°` | `3–5°` für Blendschutz auch bei tiefer Sonne |
| Maximale Sonnenhöhe | `0` (aus) | nur bei Dachüberstand über Mittag |
| Zeitfenster | `09:00`–`20:00` | im Sommer ggf. bis `21:00` |
| Nur im Fenster öffnen | **an** | überlässt Öffnen der Morgen-/Abend-Automation |
| Max. Bewölkung | `40 %` | `30–50 %` |
| Verzögerung Schließen | `15 min` | `10–20 min` |
| Verzögerung Öffnen | `30 min` | `20–45 min` |
| Schutz-Wetterzustände | `hail`, `lightning-rainy`, `snowy-rainy` | optional `lightning`, `pouring` |
| Wind-Schwelle | `55 km/h` (Bft 8, Dauerwind) | `45–70` für Rollläden · `25–35` für Markisen. Eingabe in km/h, Umrechnung automatisch |
| Vorhersage-Vorlauf | `2 h` | `1–3 h`; `0` = nur aktuelle Lage |
| Position bei Unwetter | `0` | schützt die Scheibe bei Hagel |
| Positions-Toleranz | `5 %` | Handbetrieb-Erkennung |

## Bekannte Randfälle

- Gibt es bereits eine Sonnenuntergangs-/Abend-Automation für die Rollläden,
  Zeitfenster-Ende und „Nur im Fenster öffnen = an" so wählen, dass sich beide
  nicht widersprechen.
- Manche Wetter-Integrationen (z. B. met.no) liefern keine Böen-Vorhersage
  (`wind_gust_speed` = null) – dann wird der Dauerwind geprüft, Schwelle
  entsprechend niedriger ansetzen.
- Bei HA-Neustart wird der Soll-Zustand einmalig ohne Verzögerung hergestellt.
