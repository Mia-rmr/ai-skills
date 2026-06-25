---
name: project-decomposer
description: Décompose automatiquement des projets Linear dont Mia est Lead en Epics, Stories et Tâches techniques, avec critères d'acceptation, estimations, et création directe des issues dans Linear avec les bons labels. Utilise ce skill dès que l'utilisateur mentionne "découper un projet", "créer des epics", "décomposer en stories", "planifier les tâches", "structurer un projet Linear", ou demande à organiser le travail d'un projet existant en sous-éléments — même sans dire explicitement "epic" ou "story". Ce skill s'applique aussi quand l'utilisateur pointe vers un projet Linear existant à rendre actionnable. IMPORTANT : ne s'applique qu'aux projets dont Mia est Lead.
---
 
# Project Decomposer — Linear (projets Mia)
 
Ce skill décompose les projets Linear **dont Mia est Lead** en **Epics → Stories → Tâches techniques**, crée les issues directement dans Linear avec les bons labels, hiérarchie et rattachement au projet.
 
---
 
## Workflow
 
### Étape 1 — Identifier les projets de Mia
 
1. Récupérer l'identité de Mia via `Linear:list_users` — chercher l'utilisateur dont le nom contient "Mia"
2. Lister tous les projets via `Linear:list_projects`
3. Filtrer uniquement les projets où `lead.id == mia.id`
4. Si aucun projet trouvé : informer l'utilisateur et arrêter
5. Si plusieurs projets éligibles et que l'utilisateur n'en a pas précisé un : les lister et demander lequel décomposer
6. Récupérer le projet sélectionné via `Linear:get_project`
> ⚠️ Si le projet demandé n'a pas Mia comme Lead, refuser poliment et expliquer la contrainte.
 
---
 
### Étape 2 — Préparer les labels
 
1. Récupérer les labels existants via `Linear:list_issue_labels` (filtré par team si possible)
2. Identifier les labels Epic, Story, Task (recherche insensible à la casse)
3. Pour chaque label manquant, le créer via `Linear:create_issue_label` :
   - Epic → couleur `#8B5CF6` (violet)
   - Story → couleur `#3B82F6` (bleu)
   - Task → couleur `#10B981` (vert)
4. Stocker les IDs des 3 labels pour les utiliser à la création
---
 
### Étape 3 — Analyser le contexte existant
 
1. Récupérer les issues existantes du projet via `Linear:list_issues` (filtrées par `projectId`)
2. Identifier ce qui existe déjà pour éviter les doublons
3. Si des issues existent déjà : proposer de compléter plutôt que repartir de zéro
---
 
### Étape 4 — Générer la décomposition
 
À partir du nom, de la description du projet et des issues existantes, produire une structure complète.
 
#### Structure cible
 
```
EPIC — [Nom fonctionnel]
  Description : objectif métier de l'epic
  Estimation : S / M / L / XL
  Critères d'acceptation :
    - [ ] ...
 
  STORY — [En tant que <rôle>, je veux <action> afin de <bénéfice>]
    Estimation : 1 / 2 / 3 / 5 / 8 points
    Critères d'acceptation :
      - Étant donné..., quand..., alors...
    
    TÂCHE — [Action technique courte]
      Estimation : 0.5j / 1j / 2j
```
 
#### Règles de découpage
 
- **Epic** : fonctionnalité majeure, 2–6 semaines, transverse
- **Story** : unité de valeur utilisateur, 1–5 jours, une seule personne
- **Tâche** : action atomique, <1 jour, sans dépendances internes
#### Estimations
 
| Niveau | Méthode |
|--------|---------|
| Epic   | T-shirt : XS / S / M / L / XL |
| Story  | Fibonacci : 1 / 2 / 3 / 5 / 8 / 13 points |
| Tâche  | Durée : 0.5j / 1j / 2j |
 
#### Critères d'acceptation
 
Minimum 3 critères par Story au format Gherkin simplifié :
```
Étant donné [contexte], quand [action], alors [résultat attendu]
```
 
---
 
### Étape 5 — Afficher le récapitulatif et demander confirmation
 
Présenter la structure complète en markdown AVANT toute création :
 
```
## 📦 Décomposition : [Nom du projet]
🏷️ Labels détectés/créés : Epic ✅ | Story ✅ | Task ✅
 
### EPIC 1 — [Nom]
Estimation : L | Label : Epic
Critères : ...
 
  #### Story 1.1 — En tant que...
  Estimation : 5 pts | Label : Story
  Critères : ...
  
    - Tâche A — 1j | Label : Task
    - Tâche B — 0.5j | Label : Task
 
---
📊 Total : X Epics · Y Stories · Z Tâches · ~N semaines
```
 
Puis demander : **"Je crée ces [N] issues dans Linear sur le projet [Nom] ?"**
 
---
 
### Étape 6 — Créer les issues dans Linear
 
Créer dans cet ordre strict (pour respecter la hiérarchie `parentId`) :
 
1. **Epics** via `Linear:save_issue` :
   - `title`, `description` (avec critères d'acceptation en markdown), `estimate`, `labelIds: [epicLabelId]`, `projectId`
2. **Stories** via `Linear:save_issue` :
   - Mêmes champs + `parentId: epicIssueId`, `labelIds: [storyLabelId]`
3. **Tâches** via `Linear:save_issue` :
   - Mêmes champs + `parentId: storyIssueId`, `labelIds: [taskLabelId]`
> Toutes les issues sont rattachées au projet via `projectId`.
 
---
 
### Étape 7 — Résumé final
 
```
✅ Créé dans Linear — projet "[Nom]" :
- X Epics  (label : Epic)
- Y Stories (label : Story)
- Z Tâches  (label : Task)
Charge totale estimée : ~N semaines / ~P points
```
 
---
 
## Gestion des erreurs
 
- **Projet sans Mia comme Lead** : refuser, lister les projets éligibles de Mia
- **Mia introuvable dans Linear** : demander à l'utilisateur de confirmer son nom exact dans Linear
- **Description du projet vide** : demander à l'utilisateur de décrire l'objectif en 2-3 phrases avant de décomposer
- **Échec de création d'un label** : noter l'erreur, continuer sans ce label et signaler en fin de résumé
- **Trop d'issues existantes (>20)** : proposer de compléter uniquement les niveaux manquants
