# Project Scope

## In Scope

- Ego model specification (base model selection, LoRA structure, edition system)
- Ego Pod — fully automated training pipeline
- Data Collector — reads data from specified sources (defines input format, not the data format itself)
- Corpus Compiler — transforms raw data into training corpus
- Training Engine — trains LoRA adapters based on base models
- Version Management — multi-edition output, old version overwriting
- Standalone Tool Mode — can be used independently without iFay

## Out of Scope

- Fayger (virtual runtime environment)
- Faying (loading protocol)
- FayID (identity system)
- Data format definitions for Sense/Action/Thought/Skill/Credentials (defined by sister projects)
- Protocol layer (Faying Protocol, Telepathy Protocol, etc.)

Ego only defines "what format of input I need" and "what I output." Where data comes from and how it reaches endpoints is the responsibility of sister projects.

> Analogy: Ego Pod is like a "chef" — you tell it which shelf in the fridge the ingredients are on (data source address), and it handles the cooking (training). But how the fridge is designed and how ingredients are sourced is someone else's job.
