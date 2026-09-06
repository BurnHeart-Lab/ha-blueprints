# ha-blueprints

Home-Assistant-Blueprints für das eigene Setup.

## Rollläden nach Sonnenstand & Wetter

Sonnenstandsgeführte Verschattung einer Fassade mit Unwetterschutz
(Hagel / Gewitter / Sturm, aktuell + Stundenvorhersage).

- Blueprint: [`automation/rolladen_sonnenstand_wetter.yaml`](automation/rolladen_sonnenstand_wetter.yaml)
- Ausführliche Doku & Empfehlungswerte: [`BLUEPRINT-rolladen.md`](BLUEPRINT-rolladen.md)

### Import in Home Assistant

**Einstellungen → Automationen & Szenen → Blueprints → Blueprint importieren**, dann eine der URLs:

```
https://github.com/BurnHeart-Lab/ha-blueprints/blob/main/automation/rolladen_sonnenstand_wetter.yaml
```

Danach: **Automation erstellen → Aus Blueprint → „Rollläden nach Sonnenstand & Wetter"**.
Pro Himmelsrichtung eine eigene Automation anlegen.
