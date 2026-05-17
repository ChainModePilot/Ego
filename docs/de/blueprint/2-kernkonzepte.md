# Kernkonzepte

## Ego Pod (Selbstwahrnehmungskapsel)

Ego Pod ist die Kern-Engine von Ego — eine vollautomatische Trainingspipeline. Wie eine „Kultivierungskammer": Du gibst Rohdaten hinein, und sie schmiedet automatisch ein KI-Modell, das dich versteht.

Sie erledigt drei Dinge:
1. **Sammeln** — Human-Prime-Daten aus angegebenen Quellen lesen (lokale Verzeichnisse/URLs/Cloud-Speicher/...)
2. **Schmieden** — Rohdaten zu Trainingskorpus verarbeiten, LoRA-Adapter auf einem ausgewählten Basismodell trainieren
3. **Ausgeben** — Mehrere Versionen (Editionen) des Ego-Modells erzeugen, alte Versionen überschreiben

> Analogie: Ego Pod ist wie eine „Personalisierungsfabrik". Du gibst dein Tagebuch, deinen Chatverlauf und deine Arbeitsnotizen hinein (Rohmaterialien), und sie reinigt, klassifiziert und verarbeitet diese automatisch, um letztendlich eine „Mini-Version von dir" (Fertigprodukt) herzustellen. Dieses Produkt kann auf einer Smartwatch (lite-Edition) oder einem Computer (plus-Edition) installiert werden, wie dieselbe Person in unterschiedlich großer Kleidung.

```
Datenquellen               Ego Pod                        Ausgabe
┌──────────────┐        ┌─────────────────┐         ┌──────────────┐
│ Sense-Daten   │──┐     │                 │         │ Ego-lite     │
│ Action-Daten  │──┤     │  ① Datensammlung │        │ Ego-normal   │
│ Thought-Daten │──┼───→ │  ② Korpuserstellung│──────→│ Ego-plus     │
│ Skill-Daten   │──┤     │  ③ LoRA-Training  │        │ Ego-super    │
│ Credentials  │──┘     │  ④ Multi-Ver-Ausgabe│       │ [custom...]  │
└──────────────┘        └─────────────────┘         └──────────────┘
 Verzeichnis/URL/Cloud     Vollautomatisch · Wiederholbar  Überschreibt Alt
```

## Ego-Modellstruktur

Jedes Ego = Basismodell + LoRA-Adapter

- **Basismodell**: Open-Source-Modelle mit kleiner Parameterzahl (z. B. Qwen 3.5, Gemma 4), die allgemeine Sprach- und Schlussfolgerungsfähigkeiten bereitstellen. Analogie: Das Basismodell ist wie eine „Rohbauwohnung" — sie hat grundlegende Struktur und Versorgungsleitungen, aber keinen persönlichen Stil.
- **LoRA-Adapter**: Aus Human-Prime-Daten trainiert, trägt die Persönlichkeit, Vorlieben, Erinnerungen, Wissen und Fähigkeiten des Human Prime. Analogie: LoRA ist wie dein „Inneneinrichtungsplan" — deine Lieblingsmöbel, Wandfarben und Beleuchtungsanordnung machen das Haus erst zu „deinem Zuhause".

Das Basismodell enthält keine personalisierten Informationen; LoRA ist die „Seele".

> Einen Basiswechsel kann man sich wie einen Umzug vorstellen: Du ziehst von einem Einzimmerapartment (0.8B) in eine Dreizimmerwohnung (4B) — die Wohnung wird größer, aber dein Einrichtungsstil (LoRA) muss an das neue Layout angepasst werden. Die gute Nachricht: Ego Pod übernimmt diesen „Renovierungsprozess" automatisch.

## Editionssystem

Ego bietet vier Standardversionen, angepasst an Endgeräte mit unterschiedlichen Rechenkapazitäten:

| Edition | Zielgerät | Basisgröße | Typisches Szenario |
|---------|-----------|-----------|-------------------|
| **lite** | Smartwatches, Sprachrekorder, Drohnen, IoT-Geräte | ~0.8B | Ego-lite läuft auf deiner Smartwatch, weiß, dass du um 7 Uhr aufstehst und schwarzen Kaffee bevorzugst, und sendet jeden Morgen automatisch Wetter- und Terminhinweise im selben Ton wie deine eigenen Notizen |
| **normal** | Handys, Tablets | ~2B | Ego-normal auf deinem Handy versteht deine Gesprächsgewohnheiten und verwendet beim Verfassen von E-Mails automatisch deine üblichen Formulierungen und dein Signaturformat |
| **plus** | Hochleistungs-PCs, lokale Server | ~4-9B | Ego-plus auf deinem Laptop unterstützt beim Programmieren, hat deinen Codierungsstil gelernt (Variablenbenennungskonventionen, Kommentarstil, häufige Entwurfsmuster) und gibt Vorschläge, als hättest du sie selbst geschrieben |
| **super** | Cloud-Dienste, GPU-Cluster | ~27B+ | Ego-super in der Cloud verfügt über deine vollständige Wissensbasis, kann in deinem Namen an technischen Reviews teilnehmen und Meinungen abgeben, die stark mit deinem eigenen Urteil übereinstimmen |

Benutzerdefinierte Editionen werden unterstützt — Nutzer können sie frei benennen und konfigurieren.

> Beispiel: Ein Arzt kann eine benutzerdefinierte „clinic"-Edition (Basis 4B) definieren, spezialisiert auf ambulante Szenarien, die seine Konsultationsgewohnheiten, häufige Rezeptvorlagen und seinen Patientenkommunikationsstil gelernt hat.

Jede Edition verwendet ein Basismodell unterschiedlicher Größe und hat einen eigenen, unabhängig trainierten Satz von LoRA-Adaptern.
