# ha-blueprints

Home-Assistant-Blueprints für das eigene Setup.

## Rollläden – Sonne, Wetter, Tag & Nacht

Kompletter Tagesablauf einer Rollladen-Gruppe pro Himmelsrichtung:
Guten Morgen, Verschattung nach Sonnenstand, Gute Nacht, Unwetterschutz —
integriert mit fester Prioritätsreihenfolge.

- Blueprint: [`automation/rolladen_sonnenstand_wetter.yaml`](automation/rolladen_sonnenstand_wetter.yaml)
- Ausführliche Doku & Empfehlungswerte: [`BLUEPRINT-rolladen.md`](BLUEPRINT-rolladen.md)

### Import in Home Assistant

**Einstellungen → Automationen & Szenen → Blueprints → Blueprint importieren**, dann eine der URLs:

```
https://github.com/BurnHeart-Lab/ha-blueprints/blob/main/automation/rolladen_sonnenstand_wetter.yaml
```

Danach: **Automation erstellen → Aus Blueprint → „Rollläden – Sonne, Wetter, Tag & Nacht"**.
Pro Himmelsrichtung eine eigene Automation anlegen.
