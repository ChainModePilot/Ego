# Périmètre du projet

## Inclus dans le périmètre

- Spécification du modèle Ego (sélection du modèle de base, structure LoRA, système d'éditions)
- Ego Pod — pipeline d'entraînement entièrement automatisé
- Data Collector — lit les données depuis les sources spécifiées (définit le format d'entrée, pas le format des données lui-même)
- Corpus Compiler — transforme les données brutes en corpus d'entraînement
- Training Engine — entraîne les adaptateurs LoRA à partir des modèles de base
- Gestion des versions — sortie multi-éditions, écrasement des anciennes versions
- Mode outil autonome — peut être utilisé indépendamment sans iFay

## Hors périmètre

- Fayger (environnement d'exécution virtuel)
- Faying (protocole de chargement)
- FayID (système d'identité)
- Définitions de format de données pour Sense/Action/Thought/Skill/Credentials (définies par les projets frères)
- Couche protocole (Faying Protocol, Telepathy Protocol, etc.)

Ego définit uniquement « quel format d'entrée j'ai besoin » et « ce que je produis en sortie ». D'où viennent les données et comment elles atteignent les terminaux relève de la responsabilité des projets frères.

> Analogie : Ego Pod est comme un « chef cuisinier » — vous lui indiquez sur quelle étagère du réfrigérateur se trouvent les ingrédients (adresse de la source de données), et il s'occupe de la cuisine (entraînement). Mais la conception du réfrigérateur et l'approvisionnement en ingrédients, c'est le travail de quelqu'un d'autre.
