# Projektumfang

## Im Umfang enthalten

- Ego-Modellspezifikation (Basismodellauswahl, LoRA-Struktur, Editionssystem)
- Ego Pod — vollautomatische Trainingspipeline
- Data Collector — liest Daten aus angegebenen Quellen (definiert das Eingabeformat, nicht das Datenformat selbst)
- Corpus Compiler — wandelt Rohdaten in Trainingskorpus um
- Training Engine — trainiert LoRA-Adapter basierend auf Basismodellen
- Versionsverwaltung — Multi-Editions-Ausgabe, Überschreiben alter Versionen
- Eigenständiger Werkzeugmodus — kann unabhängig von iFay verwendet werden

## Nicht im Umfang enthalten

- Fayger (virtuelle Laufzeitumgebung)
- Faying (Ladeprotokoll)
- FayID (Identitätssystem)
- Datenformatdefinitionen für Sense/Action/Thought/Skill/Credentials (werden von Schwesterprojekten definiert)
- Protokollschicht (Faying Protocol, Telepathy Protocol usw.)

Ego definiert nur „welches Eingabeformat ich benötige" und „was ich ausgebe". Woher die Daten kommen und wie sie die Endgeräte erreichen, liegt in der Verantwortung der Schwesterprojekte.

> Analogie: Ego Pod ist wie ein „Koch" — du sagst ihm, auf welchem Regal im Kühlschrank die Zutaten liegen (Datenquelladresse), und er kümmert sich ums Kochen (Training). Aber wie der Kühlschrank gestaltet ist und wie die Zutaten beschafft werden, ist die Aufgabe anderer.
