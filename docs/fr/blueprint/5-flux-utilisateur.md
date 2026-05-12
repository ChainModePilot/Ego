# Flux utilisateur

## Pour les utilisateurs quotidiens

```
Étape 1 : Installer Ego Pod
  $ pip install ego-pod

Étape 2 : Initialiser la configuration
  $ ego init
  → Guide interactif : sélectionner le modèle de base, configurer les chemins des sources de données, choisir les éditions
  → Comme configurer un nouveau téléphone, il suffit de suivre les instructions étape par étape

Étape 3 : Lancer l'entraînement
  $ ego train
  → Collecte automatique des données → construction du corpus → entraînement LoRA → sortie Ego multi-versions
  → Le temps de boire un café, votre IA personnalisée est prête

Étape 4 : Utiliser Ego
  $ ego run --edition normal
  → Charger base + LoRA, démarrer un modèle d'IA aligné avec vous
  → Essayez de discuter avec, voyez si ça « vous ressemble »

Étape 5 : Mettre à jour Ego (après modification des données)
  $ ego train --update
  → Collecte incrémentale des données → ré-entraînement → écrasement de l'ancienne version
  → Vous avez appris quelque chose de nouveau récemment ? Forgez à nouveau, et Ego rattrape son retard
```

## Intégration iFay

Le corps iFay indique à Ego les adresses des sources de données via des fichiers de configuration. Ego Pod complète automatiquement l'entraînement, et les adaptateurs LoRA produits sont chargés par Fayger.

> Par exemple : Votre iFay fonctionne sur votre téléphone, synchronisant périodiquement les nouvelles données vers un répertoire désigné, puis déclenchant le ré-entraînement d'Ego Pod. Une fois l'entraînement terminé, le nouvel adaptateur LoRA est automatiquement poussé vers votre téléphone, et Ego « évolue » — l'ensemble du processus est invisible pour vous.
