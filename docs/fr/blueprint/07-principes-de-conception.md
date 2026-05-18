# Principes de conception

1. **Prêt à l'emploi** — Les utilisateurs ordinaires peuvent démarrer avec trois commandes après `pip install`. Aucune connaissance en apprentissage automatique ni configuration de pilotes GPU requise.
2. **Entièrement automatisé** — De la collecte des données à la sortie du modèle, aucune intervention manuelle nécessaire. Comme une machine à laver : mettez le linge, appuyez sur démarrer, récupérez le linge propre.
3. **Adaptation multi-versions** — Un seul entraînement produit plusieurs versions pour différentes capacités de calcul. Le même « vous » peut simultanément vivre sur une montre connectée, un téléphone et un ordinateur.
4. **Exécution reproductible** — Ré-entraînez après une mise à jour des données, et Ego évolue en conséquence. À mesure que vous grandissez, Ego grandit aussi.
5. **Base interchangeable** — Changez de modèle de base quand de nouveaux modèles sortent, ré-entraînez LoRA. Comme mettre à jour le processeur d'un téléphone — les performances s'améliorent mais la « personnalité » reste la même.
6. **Extensible par plugins** — Les adaptateurs de sources de données, les modèles de base et les éditions sont tous extensibles. La communauté peut contribuer de nouveaux plugins de sources de données (ex. adaptateur Notion, importateur d'historique de chat WhatsApp).
7. **Confidentialité d'abord** — Tout l'entraînement peut être effectué localement, les données ne quittent jamais l'appareil. Votre journal intime, votre historique de conversations et vos notes de travail restent pour toujours sur votre propre ordinateur.
