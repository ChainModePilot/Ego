# Concepts clés

## Ego Pod (Capsule de conscience de soi)

Ego Pod est le moteur central d'Ego — un pipeline d'entraînement entièrement automatisé. Comme une « chambre de culture », vous y mettez des données brutes, et il forge automatiquement un modèle d'IA qui vous comprend.

Il fait trois choses :
1. **Collecter** — Lire les données de l'hôte depuis les sources spécifiées (répertoires locaux/URLs/stockage cloud/...)
2. **Forger** — Transformer les données brutes en corpus d'entraînement, entraîner des adaptateurs LoRA sur un modèle de base sélectionné
3. **Produire** — Générer plusieurs versions (éditions) du modèle Ego, en écrasant les anciennes versions

> Analogie : Ego Pod est comme une « usine de personnalisation ». Vous y introduisez votre journal intime, votre historique de conversations et vos notes de travail (matières premières), et il les nettoie, les classe et les traite automatiquement, produisant finalement une « mini version de vous » (produit fini). Ce produit peut être installé sur une montre connectée (édition lite) ou un ordinateur (édition plus), comme la même personne portant des vêtements de tailles différentes.

```
Sources de données         Ego Pod                        Sortie
┌──────────────┐        ┌─────────────────┐         ┌──────────────┐
│ Sense Data    │──┐     │                 │         │ Ego-lite     │
│ Action Data   │──┤     │  ① Collecte     │         │ Ego-normal   │
│ Thought Data  │──┼───→ │  ② Construction │───────→ │ Ego-plus     │
│ Skill Data    │──┤     │  ③ Entraînement │         │ Ego-super    │
│ Credentials  │──┘     │  ④ Sortie multi-v│         │ [custom...]  │
└──────────────┘        └─────────────────┘         └──────────────┘
 Répertoire/URL/Cloud      Entièrement auto · Reproductible  Écrase l'ancien
```

## Structure du modèle Ego

Chaque Ego = Modèle de base + Adaptateur LoRA

- **Modèle de base** : Modèles open source à petit nombre de paramètres (ex. Qwen 3.5, Gemma 4), fournissant des capacités générales de langage et de raisonnement. Analogie : Le modèle de base est comme un « appartement brut » — il a la structure de base et les installations, mais aucun style personnel.
- **Adaptateur LoRA** : Entraîné à partir des données de l'hôte, portant la personnalité, les préférences, les souvenirs, les connaissances et les compétences de l'hôte. Analogie : LoRA est comme votre « plan de décoration intérieure » — vos meubles préférés, les couleurs des murs et l'agencement de l'éclairage sont ce qui fait de la maison « votre chez-vous ».

Le modèle de base ne contient aucune information personnalisée ; LoRA est l'« âme ».

> Changer de modèle de base, c'est comme déménager : Vous passez d'un studio (0.8B) à un trois-pièces (4B) — la maison est plus grande, mais votre style intérieur (LoRA) doit être ré-adapté au nouveau plan. La bonne nouvelle, c'est qu'Ego Pod gère automatiquement ce processus de « rénovation ».

## Système d'éditions

Ego fournit quatre versions par défaut, adaptées à des terminaux aux capacités de calcul différentes :

| Édition | Appareil cible | Échelle du modèle de base | Scénario typique |
|---------|---------------|--------------------------|-----------------|
| **lite** | Montres connectées, enregistreurs vocaux, drones, appareils IoT | ~0.8B | Ego-lite fonctionne sur votre montre connectée, sait que vous vous réveillez à 7h et préférez le café noir, et vous envoie automatiquement chaque matin la météo et les rappels de planning dans le même ton que vos propres mémos |
| **normal** | Téléphones, tablettes | ~2B | Ego-normal sur votre téléphone comprend vos habitudes de conversation, utilise automatiquement vos formulations courantes et votre format de signature lors de la rédaction d'e-mails |
| **plus** | PC haute performance, serveurs locaux | ~4-9B | Ego-plus sur votre ordinateur portable vous assiste en programmation, ayant appris votre style de code (conventions de nommage des variables, style de commentaires, design patterns courants), donnant des suggestions comme si vous les aviez écrites vous-même |
| **super** | Services cloud, clusters GPU | ~27B+ | Ego-super dans le cloud possède votre base de connaissances complète, peut assister à des revues techniques en votre nom, donnant des avis hautement cohérents avec votre propre jugement |

Les éditions personnalisées sont prises en charge — les utilisateurs peuvent les nommer et les configurer librement.

> Par exemple : Un médecin peut définir une édition personnalisée « clinique » (base 4B), spécialisée pour les scénarios de consultation, ayant appris ses habitudes de consultation, ses modèles d'ordonnance courants et son style de communication avec les patients.

Chaque édition utilise un modèle de base d'échelle différente et possède son propre ensemble d'adaptateurs LoRA entraînés indépendamment.
