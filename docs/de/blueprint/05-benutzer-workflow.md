# Benutzer-Workflow

## Für alltägliche Nutzer

```
Schritt 1: Ego Pod installieren
  $ pip install ego-pod

Schritt 2: Konfiguration initialisieren
  $ ego init
  → Interaktiver Assistent: Basismodell auswählen, Datenquellpfade konfigurieren, Editionen wählen
  → Wie das Einrichten eines neuen Handys, einfach den Anweisungen Schritt für Schritt folgen

Schritt 3: Training starten
  $ ego train
  → Automatische Datensammlung → Korpuserstellung → LoRA-Training → Multi-Versions-Ego-Ausgabe
  → In der Zeit, die du für einen Kaffee brauchst, ist deine personalisierte KI fertig

Schritt 4: Ego verwenden
  $ ego run --edition normal
  → Basis + LoRA laden, ein auf dich abgestimmtes KI-Modell starten
  → Probiere es aus und chatte damit, schau ob es „nach dir klingt"

Schritt 5: Ego aktualisieren (nach Datenänderungen)
  $ ego train --update
  → Inkrementelle Datensammlung → Neutraining → alte Version überschreiben
  → Kürzlich etwas Neues gelernt? Erneut schmieden, und Ego holt auf
```

## iFay-Integration

Der iFay-Körper teilt Ego die Datenquelladressen über Konfigurationsdateien mit. Ego Pod schließt das Training automatisch ab, und die erzeugten LoRA-Adapter werden von Fayger geladen.

> Beispiel: Dein iFay läuft auf deinem Handy und synchronisiert regelmäßig neue Daten in ein bestimmtes Verzeichnis, woraufhin Ego Pod zum Neutraining ausgelöst wird. Nach Abschluss des Trainings wird der neue LoRA-Adapter automatisch auf dein Handy übertragen, und Ego „entwickelt sich weiter" — der gesamte Prozess ist für dich unsichtbar.
