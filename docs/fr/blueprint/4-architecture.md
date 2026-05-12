# Architecture

## Architecture globale

```
┌─────────────────────────────────────────────────────────────────┐
│                    Ego Pod (Capsule de conscience de soi)        │
│                                                                 │
│  ┌───────────┐    ┌───────────┐    ┌───────────┐    ┌────────┐ │
│  │  Data      │───→│  Corpus   │───→│  Training │───→│Version │ │
│  │ Collector  │    │ Compiler  │    │  Engine   │    │ Vault  │ │
│  └───────────┘    └───────────┘    └───────────┘    └────────┘ │
│       ↑                                                  ↓      │
│  ┌─────────┐                                      ┌──────────┐ │
│  │  Source  │                                      │ Ego      │ │
│  │  Config  │                                      │ Editions │ │
│  └─────────┘                                      └──────────┘ │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                  Config Center                            │   │
│  │  Sélection base · Déf. éditions · Params entraînement · Mapping sources · Planning │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

## Détail des modules

### 1. Data Collector

Lit les données de l'hôte depuis les sources de données configurées.

**Types de sources de données pris en charge :**
- Répertoires locaux (chemins du système de fichiers)
- URLs (points d'accès HTTP/HTTPS)
- Stockage cloud (S3, OSS, Google Drive, etc., extensible via plugins)
- Adaptateurs personnalisés (les utilisateurs peuvent écrire des plugins de collecte)

**Catégories de données collectées (correspondant à l'architecture iFay) :**

| Catégorie de données | Module iFay | Exemple concret |
|---------------------|-------------|-----------------|
| Données de perception | Sense | Itinéraires d'exercice quotidiens, distribution du temps d'utilisation du téléphone, emplacements des restaurants fréquentés, habitudes de sommeil |
| Données comportementales | Action | Habitudes d'utilisation du téléphone (toujours consulter les messages avant les e-mails), raccourcis fréquemment utilisés, scripts d'automatisation |
| Données cognitives | Thought | Notes de lecture, journaux de travail, réflexions d'apprentissage, évaluations et processus de réflexion sur les solutions techniques |
| Données de compétences | Skill | Langages de programmation et niveaux de maîtrise, recettes favorites, instruments joués et répertoire |
| Données d'accréditation | Credentials | Identifiants d'identité, périmètre d'autorisation (ex. peut envoyer des e-mails en votre nom mais ne peut pas effectuer de virements), limites de permissions |

> Par exemple : La configuration du Collector d'une traductrice freelance pourrait ressembler à ceci — Sense pointe vers sa mémoire de traduction (répertoire local), Action pointe vers les journaux d'opération de son outil de TAO (URL), Thought pointe vers ses notes terminologiques (stockage cloud), Skill pointe vers sa liste de paires de langues et spécialisations (fichier local).

Le Collector ne se soucie pas du format interne des données (c'est défini par les projets frères) — il se soucie uniquement de « où récupérer » et « quelle catégorie de données a été récupérée ».

### 2. Corpus Compiler

Transforme les données brutes collectées en corpus utilisable pour l'entraînement LoRA.

**Responsabilités principales :**
- **Nettoyage et anonymisation des données** — En particulier pour les données liées aux Credentials. Exemple : Votre clé API `sk-abc123...` est remplacée par `[REDACTED]`, garantissant qu'aucune information sensible ne se trouve dans les données d'entraînement
- **Standardisation du format** — Unification dans des formats acceptables par les frameworks d'entraînement. Exemple : Une note de lecture « Ce livre traite du théorème CAP dans les systèmes distribués... » est transformée en `{"instruction": "Expliquer le théorème CAP", "output": "Le théorème CAP dans les systèmes distribués..."}`
- **Classification du corpus** — Regroupement par couches LoRA. Exemple : « J'aime boire un Americano » va dans la couche préférences, « Voyage au Japon en 2024 » dans la couche mémoire, « Le mécanisme GIL de Python est... » dans la couche connaissances
- **Filtrage qualité** — Suppression des données bruitées et des doublons. Exemple : Les enregistrements de chat en double ne conservent qu'une seule copie, les fragments trop courts (comme « ok », « d'accord ») sont filtrés
- **Augmentation des données** — Extension du corpus par des modèles ou de la réécriture si nécessaire. Exemple : La donnée brute « Recommande une chanson » est augmentée en « Exprime-le différemment : Recommande une chanson », « Reformule : Recommande une chanson »

**Formats de sortie :**
- Jeu de données standard de fine-tuning par instructions (triplets instruction/input/output)
- Jeu de données au format conversation (conversation multi-tours)
- Sous-jeux de données regroupés par couches LoRA


### 3. Training Engine (Trainer)

Entraîne les adaptateurs LoRA à partir du modèle de base sélectionné et du corpus compilé.

**Auto-adaptation multi-backend :**

Le Trainer détecte automatiquement votre environnement matériel et sélectionne le backend d'entraînement optimal :

| Priorité | Backend | Environnement | Vitesse |
|----------|---------|---------------|---------|
| 1 | Unsloth | GPU NVIDIA (CUDA) | Le plus rapide (accélération 2x) |
| 2 | HuggingFace PEFT | GPU NVIDIA (CUDA) | Vitesse standard |
| 3 | Apple MLX | Mac (Apple Silicon) | Accélération native |
| 4 | HuggingFace PEFT | CPU uniquement | Plus lent mais universel |
| 5 | Stub | Pas de dépendances ML | Mode simulation (dev/test) |

> Vous n'avez pas besoin de vous soucier du backend utilisé — Ego Pod sélectionne automatiquement. Si vous avez une RTX 4090, il utilise Unsloth pour l'accélération ; si vous êtes sur un MacBook Pro M3, il utilise MLX ; si vous voulez juste essayer le flux d'abord, il utilise le mode Stub pour un entraînement simulé.

**Flux d'entraînement :**
1. Charger le modèle de base (quantification 4 bits)
2. Entraîner les adaptateurs séquentiellement par couche LoRA
3. Valider les résultats d'entraînement (évaluation automatique de l'alignement)
4. Générer les combinaisons base+LoRA pour chaque édition

**Stratégie d'entraînement multi-éditions :**
- Chaque édition utilise un modèle de base d'échelle différente
- Même corpus, LoRA entraîné séparément pour différentes bases
- Plusieurs éditions peuvent être entraînées en parallèle

> Par exemple : Sur un ordinateur de bureau avec GPU, lancer `ego train` fera qu'Ego Pod entraîne simultanément trois versions de LoRA pour votre montre connectée (lite), téléphone (normal) et ordinateur (plus). L'ensemble du processus prend environ 10 à 30 minutes, selon le volume de données.

### 4. Version Vault

Gère les versions du modèle Ego et les sorties.

**Responsabilités :**
- Stocker les adaptateurs LoRA produits par chaque session d'entraînement
- Gérer les fichiers de modèle à travers plusieurs éditions
- Prendre en charge le retour en arrière de version (conserver les versions historiques)
- Écraser les anciennes versions (comportement par défaut)
- Exporter sous forme de packages Ego distribuables

> Par exemple : Vous avez entraîné un Ego la semaine dernière (v0.1.0), puis ajouté de nouvelles données et ré-entraîné cette semaine (v0.2.0). Vous trouvez que le style de réponse de la nouvelle version a changé — ça ne vous ressemble plus. Vous pouvez revenir à la v0.1.0 en un clic, comme rétrograder le système d'exploitation d'un téléphone.

**Structure du package Ego :**
```
ego-output/
├── manifest.json              # Métadonnées Ego (version, info base, liste des éditions)
├── lite/
│   ├── config.json            # Config de l'édition (modèle de base, quantification, exigences matérielles)
│   └── adapters/
│       └── ego-lora.safetensors  # Poids de l'adaptateur LoRA
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
└── [custom-name]/             # Édition définie par l'utilisateur
    ├── config.json
    └── adapters/
        └── ego-lora.safetensors
```

### 5. Config Center

Gère de manière centralisée toutes les configurations d'Ego Pod.

**Éléments de configuration :**

```yaml
# ego-config.yaml exemple

# Mapping des sources de données — indiquer à Ego Pod où trouver vos données
sources:
  sense:
    type: "local"
    path: "/data/ifay/sense/"          # Votre répertoire de données de perception
  action:
    type: "url"
    endpoint: "https://api.example.com/action-logs"  # API de journaux comportementaux
  thought:
    type: "local"
    path: "/data/ifay/thought/"        # Vos notes et base de connaissances
  skill:
    type: "s3"
    bucket: "ifay-skills"              # Données de compétences dans le cloud
    prefix: "user-001/"
  credentials:
    type: "local"
    path: "/data/ifay/credentials/"    # Données d'accréditation (anonymisées automatiquement)

# Paramètres d'entraînement — généralement pas besoin de modification, les valeurs par défaut conviennent à la plupart des scénarios
training:
  method: "qlora"
  quantization: "4bit"
  lora_rank: 16
  lora_alpha: 32
  learning_rate: 2e-4
  epochs: 3
  batch_size: 4

# Définitions des éditions — quelles versions vous souhaitez produire
editions:
  lite:
    base: "qwen3.5-0.8B"
    description: "Montres connectées, enregistreurs vocaux, drones, IoT"
  normal:
    base: "qwen3.5-2B"
    description: "Téléphones, tablettes"
  plus:
    base: "qwen3.5-4B"
    description: "PC haute performance, serveurs locaux"
  super:
    base: "qwen3.5-27B"
    description: "Services cloud, clusters GPU"
  # Vous pouvez ajouter des éditions personnalisées ici, ex. :
  # clinic:
  #   base: "qwen3.5-4B"
  #   description: "Édition consultation clinique"

# Stratégie de planification — quand ré-entraîner automatiquement
schedule:
  auto_update: true
  interval: "weekly"          # Forge automatique hebdomadaire
  trigger: "data_change"      # Ou manual / cron (tâche planifiée)
```
