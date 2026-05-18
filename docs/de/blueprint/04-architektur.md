# Architektur

## Gesamtarchitektur

```
┌─────────────────────────────────────────────────────────────────┐
│                    Ego Pod (Selbstwahrnehmungskapsel)            │
│                                                                 │
│  ┌───────────┐    ┌───────────┐    ┌───────────┐    ┌────────┐ │
│  │  Daten-    │───→│  Korpus-  │───→│  Training-│───→│Version-│ │
│  │ sammler   │    │ compiler  │    │  engine   │    │ tresor │ │
│  └───────────┘    └───────────┘    └───────────┘    └────────┘ │
│       ↑                                                  ↓      │
│  ┌─────────┐                                      ┌──────────┐ │
│  │ Quell-   │                                      │ Ego      │ │
│  │ config   │                                      │ Editionen│ │
│  └─────────┘                                      └──────────┘ │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                  Konfigurationszentrale                    │   │
│  │  Basisauswahl · Editionsdefs · Trainingsparams · Quellzuordnung · Zeitplan │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

## Moduldetails

### 1. Datensammler (Data Collector)

Liest Human-Prime-Daten aus konfigurierten Datenquellen.

**Unterstützte Datenquellentypen:**
- Lokale Verzeichnisse (Dateisystempfade)
- URLs (HTTP/HTTPS-Endpunkte)
- Cloud-Speicher (S3, OSS, Google Drive usw., erweiterbar über Plugins)
- Benutzerdefinierte Adapter (Nutzer können Sammlungs-Plugins schreiben)

**Gesammelte Datenkategorien (entsprechend der iFay-Architektur):**

| Datenkategorie | iFay-Modul | Praxisbeispiel |
|---------------|------------|----------------|
| Wahrnehmungsdaten | Sense | Tägliche Bewegungsrouten, Handynutzungszeit-Verteilung, häufig besuchte Restaurantstandorte, Schlafmuster |
| Verhaltensdaten | Action | Handy-Bediengewohnheiten (immer Nachrichten vor E-Mails prüfen), häufig genutzte Tastenkürzel, Automatisierungsskripte |
| Kognitionsdaten | Thought | Lesenotizen, Arbeitstagebücher, Lernreflexionen, Bewertungen und Denkprozesse zu technischen Lösungen |
| Fähigkeitsdaten | Skill | Programmiersprachen und Kenntnisstufen, Lieblingsrezepte, gespielte Instrumente und Repertoire |
| Berechtigungsdaten | Credentials | Identitätskennungen, Autorisierungsumfang (z. B. kann E-Mails in deinem Namen senden, aber keine Überweisungen tätigen), Berechtigungsgrenzen |

> Beispiel: Die Collector-Konfiguration einer freiberuflichen Übersetzerin könnte so aussehen — Sense zeigt auf ihren Translation Memory (lokales Verzeichnis), Action zeigt auf ihre CAT-Tool-Betriebsprotokolle (URL), Thought zeigt auf ihre Terminologienotizen (Cloud-Speicher), Skill zeigt auf ihre Sprachpaare und Spezialisierungsliste (lokale Datei).

Der Collector kümmert sich nicht um das interne Format der Daten (das wird von Schwesterprojekten definiert) — er kümmert sich nur darum, „wo abgerufen wird" und „welche Kategorie von Daten abgerufen wurde".

### 2. Korpus-Compiler (Corpus Compiler)

Wandelt gesammelte Rohdaten in für LoRA-Training verwendbaren Korpus um.

**Kernaufgaben:**
- **Datenbereinigung & Anonymisierung** — Besonders für Credentials-bezogene Daten. Beispiel: Dein API-Schlüssel `sk-abc123...` wird durch `[REDACTED]` ersetzt, um sicherzustellen, dass keine sensiblen Informationen in den Trainingsdaten enthalten sind
- **Formatstandardisierung** — Vereinheitlichung in von Trainingsframeworks akzeptierte Formate. Beispiel: Eine Lesenotiz „Dieses Buch handelt vom CAP-Theorem in verteilten Systemen..." wird zu `{"instruction": "Erkläre das CAP-Theorem", "output": "Das CAP-Theorem in verteilten Systemen..."}`
- **Korpusklassifizierung** — Gruppierung nach LoRA-Schichten. Beispiel: „Ich trinke gerne Americano" kommt in die Präferenzschicht, „2024 nach Japan gereist" in die Erinnerungsschicht, „Pythons GIL-Mechanismus ist..." in die Wissensschicht
- **Qualitätsfilterung** — Rausch-Daten und Duplikate entfernen. Beispiel: Doppelte Chat-Einträge behalten nur eine Kopie, zu kurze Fragmente (wie „ok", „klar") werden herausgefiltert
- **Datenanreicherung** — Korpus bei Bedarf durch Vorlagen oder Umschreibungen erweitern. Beispiel: Rohdaten „Empfiehl ein Lied" werden erweitert zu „Drücke es anders aus: Empfiehl ein Lied", „Formuliere um: Empfiehl ein Lied"

**Ausgabeformate:**
- Standard-Instruktions-Feintuning-Datensatz (Instruction/Input/Output-Tripel)
- Konversationsformat-Datensatz (Mehrrunden-Konversation)
- Nach LoRA-Schichten gruppierte Teildatensätze


### 3. Training Engine (Trainer)

Trainiert LoRA-Adapter basierend auf dem ausgewählten Basismodell und dem kompilierten Korpus.

**Multi-Backend-Auto-Anpassung:**

Der Trainer erkennt automatisch deine Hardware-Umgebung und wählt das optimale Trainings-Backend:

| Priorität | Backend | Umgebung | Geschwindigkeit |
|-----------|---------|----------|-----------------|
| 1 | Unsloth | NVIDIA GPU (CUDA) | Am schnellsten (2x Beschleunigung) |
| 2 | HuggingFace PEFT | NVIDIA GPU (CUDA) | Standardgeschwindigkeit |
| 3 | Apple MLX | Mac (Apple Silicon) | Native Beschleunigung |
| 4 | HuggingFace PEFT | Nur CPU | Langsamer, aber universell |
| 5 | Stub | Keine ML-Abhängigkeiten | Simulationsmodus (Entwicklung/Test) |

> Du musst dir keine Gedanken machen, welches Backend verwendet wird — Ego Pod wählt automatisch. Wenn du eine RTX 4090 hast, nutzt es Unsloth zur Beschleunigung; wenn du ein MacBook Pro M3 hast, nutzt es MLX; wenn du nur den Ablauf ausprobieren möchtest, nutzt es den Stub-Modus für simuliertes Training.

**Trainingsablauf:**
1. Basismodell laden (4-Bit-Quantisierung)
2. Adapter sequenziell nach LoRA-Schichten trainieren
3. Trainingsergebnisse validieren (automatische Alignment-Bewertung)
4. Basis+LoRA-Kombinationen für jede Edition generieren

**Multi-Editions-Trainingsstrategie:**
- Jede Edition verwendet ein Basismodell unterschiedlicher Größe
- Gleicher Korpus, LoRA wird separat für verschiedene Basen trainiert
- Mehrere Editionen können parallel trainiert werden

> Beispiel: Auf einem Desktop mit GPU führt `ego train` dazu, dass Ego Pod gleichzeitig drei LoRA-Versionen für deine Smartwatch (lite), dein Handy (normal) und deinen Computer (plus) trainiert. Der gesamte Prozess dauert etwa 10-30 Minuten, abhängig vom Datenvolumen.

### 4. Versionstresor (Version Vault)

Verwaltet Ego-Modellversionen und Ausgaben.

**Aufgaben:**
- LoRA-Adapter aus jedem Trainingslauf speichern
- Modelldateien über mehrere Editionen hinweg verwalten
- Versions-Rollback unterstützen (historische Versionen aufbewahren)
- Alte Versionen überschreiben (Standardverhalten)
- Als verteilbare Ego-Pakete exportieren

> Beispiel: Du hast letzte Woche ein Ego trainiert (v0.1.0), dann neue Daten hinzugefügt und diese Woche neu trainiert (v0.2.0). Du stellst fest, dass der Antwortstil der neuen Version sich verändert hat — sie klingt nicht mehr nach dir. Du kannst mit einem Klick auf v0.1.0 zurückrollen, wie ein Betriebssystem-Downgrade beim Handy.

**Ego-Paketstruktur:**
```
ego-output/
├── manifest.json              # Ego-Metadaten (Version, Basisinfo, Editionsliste)
├── lite/
│   ├── config.json            # Editionskonfiguration (Basismodell, Quantisierung, Hardwareanforderungen)
│   └── adapters/
│       └── ego-lora.safetensors  # LoRA-Adaptergewichte
├── normal/
│   ├── config.json
│   └── adapters/
│       └── ego-lora.safetensors
├── plus/
│   ├── config.json
│   └── adapters/
│       └── ego-lora.safetensors
├── super/
│   ├── config.json
│   └── adapters/
│       └── ego-lora.safetensors
└── [custom-name]/             # Benutzerdefinierte Edition
    ├── config.json
    └── adapters/
        └── ego-lora.safetensors
```

### 5. Konfigurationszentrale (Config Center)

Verwaltet zentral alle Ego Pod-Konfigurationen.

**Konfigurationselemente:**

```yaml
# ego-config.yaml Beispiel

# Datenquellenzuordnung — sag Ego Pod, wo deine Daten zu finden sind
sources:
  sense:
    type: "local"
    path: "/data/ifay/sense/"          # Dein Wahrnehmungsdaten-Verzeichnis
  action:
    type: "url"
    endpoint: "https://api.example.com/action-logs"  # Verhaltensprotokoll-API
  thought:
    type: "local"
    path: "/data/ifay/thought/"        # Deine Notizen und Wissensbasis
  skill:
    type: "s3"
    bucket: "ifay-skills"              # Cloud-Fähigkeitsdaten
    prefix: "user-001/"
  credentials:
    type: "local"
    path: "/data/ifay/credentials/"    # Berechtigungsdaten (automatisch anonymisiert)

# Trainingsparameter — normalerweise keine Änderung nötig, Standardwerte passen für die meisten Szenarien
training:
  method: "qlora"
  quantization: "4bit"
  lora_rank: 16
  lora_alpha: 32
  learning_rate: 2e-4
  epochs: 3
  batch_size: 4

# Editionsdefinitionen — welche Versionen du erzeugen möchtest
editions:
  lite:
    base: "qwen3.5-0.8B"
    description: "Smartwatches, Sprachrekorder, Drohnen, IoT"
  normal:
    base: "qwen3.5-2B"
    description: "Handys, Tablets"
  plus:
    base: "qwen3.5-4B"
    description: "Hochleistungs-PCs, lokale Server"
  super:
    base: "qwen3.5-27B"
    description: "Cloud-Dienste, GPU-Cluster"
  # Du kannst hier benutzerdefinierte Editionen hinzufügen, z. B.:
  # clinic:
  #   base: "qwen3.5-4B"
  #   description: "Ambulanz-Edition"

# Zeitplanstrategie — wann automatisch neu trainiert werden soll
schedule:
  auto_update: true
  interval: "weekly"          # Wöchentlich automatisch schmieden
  trigger: "data_change"      # Oder manual / cron (geplante Aufgabe)
```
