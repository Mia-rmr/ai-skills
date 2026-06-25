---
name: linear-prioritizer
description: Priorise automatiquement les projets Linear dont Mia est Lead, en les scorant selon des critères pondérés (valeur ajoutée utilisateur, urgence, effort, risque). Utilise ce skill dès que l'utilisateur mentionne "prioriser", "classer", "ordonner" ou "quel projet traiter en premier" dans un contexte Linear, ou demande une vue d'ensemble des projets de Mia — mais UNIQUEMENT si au moins 3 projets avec Mia en Lead sont trouvés dans Linear. Si moins de 3 projets sont trouvés, ne pas lancer la priorisation et en informer l'utilisateur.
---
 
# Linear Project Prioritizer
 
Priorise les projets Linear dont **Mia est Lead** en les scorant selon une grille de critères pondérés, à partir des titres et descriptions des projets.
 
---
 
## Workflow
 
### Étape 1 — Récupérer les projets
 
Via le MCP Linear, récupérer tous les projets du workspace, puis filtrer ceux dont le Lead est **Mia**.
 
Pour chaque projet, extraire :
- `name` (titre)
- `description`
- `state` (statut)
- `targetDate` (date cible si disponible)
- `lead` (pour filtrer)
> **Condition de déclenchement** : Si moins de 3 projets avec Mia en Lead sont trouvés, ne pas lancer la priorisation. Informer l'utilisateur : *"Seulement [N] projet(s) trouvé(s) avec Mia en Lead — la priorisation nécessite au moins 3 projets pour être pertinente."* et s'arrêter.
 
---
 
### Étape 2 — Scorer chaque projet
 
Appliquer la grille de scoring ci-dessous à chaque projet, **en te basant uniquement sur le titre et la description**. Si l'information est absente ou ambiguë pour un critère, attribuer un score neutre (5/10) et le signaler dans l'explication.
 
#### Grille de scoring (scores de 1 à 10)
 
| Critère | Poids | Description |
|---|---|---|
| **Valeur ajoutée utilisateur** | 40% | Score élevé (8-10) si la feature est directement visible ou utilisable par des utilisateurs finaux lambda (ex : nouvelle UI, onboarding, dashboard client, notifications, flux de paiement). Score moyen (4-7) si l'impact utilisateur est indirect. Score faible (1-3) si c'est purement interne/technique (infra, refactoring, tooling). |
| **Urgence** | 30% | Score élevé si une `targetDate` proche est mentionnée, ou si la description évoque un blocage, une dépendance externe, ou une deadline client. Score faible si aucune contrainte temporelle n'est mentionnée. |
| **Effort (inversé)** | 20% | Score élevé si le projet semble rapide/simple à livrer. Score faible si la description évoque une complexité importante, de nombreuses dépendances, ou un gros chantier. |
| **Risque (inversé)** | 10% | Score élevé si peu de risques identifiés. Score faible si la description mentionne des incertitudes techniques, de l'expérimentation, ou des dépendances critiques. |
 
#### Calcul du score final
 
```
Score final = (Valeur × 0.4) + (Urgence × 0.3) + (Effort × 0.2) + (Risque × 0.1)
```
 
---
 
### Étape 3 — Présenter les résultats
 
Présenter une liste triée du score le plus élevé au plus faible, avec pour chaque projet :
 
```
## 🥇 [Nom du projet] — Score : X.X/10
 
| Critère              | Score | Poids | Contribution |
|----------------------|-------|-------|--------------|
| Valeur utilisateur   | X/10  | 40%   | X.X          |
| Urgence              | X/10  | 30%   | X.X          |
| Effort (inversé)     | X/10  | 20%   | X.X          |
| Risque (inversé)     | X/10  | 10%   | X.X          |
 
**Pourquoi ce rang ?** [2-3 phrases expliquant les points forts et faibles du projet selon les critères. Mentionner explicitement si un score est estimé faute d'info.]
```
 
Terminer par un **tableau de synthèse** récapitulant tous les projets classés par ordre de priorité :
 
| Rang | Projet | Score final | Point fort | Point faible |
|------|--------|-------------|------------|--------------|
| 🥇 1 | Nom du projet | X.X/10 | Critère le plus élevé | Critère le plus faible |
| 🥈 2 | ... | ... | ... | ... |
| 🥉 3 | ... | ... | ... | ... |
 
---
 
## Ajustement des critères
 
Si l'utilisateur demande à modifier les poids ou les critères, appliquer les nouveaux paramètres et recalculer. Les poids doivent toujours totaliser 100%.
 
Exemples de demandes valides :
- "Augmente le poids de l'urgence à 40%"
- "Ignore le risque"
- "Ajoute un critère : alignement stratégique"
---
 
## Signaux pour la valeur ajoutée utilisateur
 
Mots-clés indiquant une **valeur élevée** (utilisateurs finaux) :
`utilisateur`, `user`, `client`, `UI`, `interface`, `onboarding`, `dashboard`, `notification`, `paiement`, `signup`, `profil`, `frontend`, `expérience`, `flux`, `parcours`, `landing`, `mobile`, `visible`, `public`
 
Mots-clés indiquant une **valeur faible** (interne/technique) :
`infra`, `infrastructure`, `refactoring`, `migration`, `CI/CD`, `pipeline`, `base de données`, `backend`, `API interne`, `tooling`, `monitoring`, `logs`, `dette technique`
 
En l'absence de ces signaux, scorer 5/10 et le noter dans l'explication.
