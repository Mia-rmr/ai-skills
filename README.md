# Skills Linear — Documentation

Ce README décrit les deux skills disponibles pour gérer les projets Linear de **Mia**.

---

## 1. `linear-prioritizer` — Priorisation des projets

### Objectif

Ce skill analyse automatiquement les projets Linear dont Mia est Lead et les classe par ordre de priorité selon une grille de scoring pondérée.

### Déclenchement

Utiliser ce skill quand l'utilisateur demande à :
- prioriser, classer, ou ordonner des projets
- savoir quel projet traiter en premier
- avoir une vue d'ensemble des projets de Mia

> **Prérequis** : au moins 3 projets avec Mia en Lead dans Linear.

### Critères de scoring

| Critère | Poids | Logique |
|---|---|---|
| Valeur ajoutée utilisateur | 40% | Élevé si impact direct sur l'utilisateur final (UI, onboarding, paiement…) |
| Urgence | 30% | Élevé si deadline proche, blocage ou dépendance externe mentionnée |
| Effort (inversé) | 20% | Élevé si le projet semble simple et rapide à livrer |
| Risque (inversé) | 10% | Élevé si peu d'incertitudes techniques identifiées |

**Formule :** `Score = (Valeur × 0.4) + (Urgence × 0.3) + (Effort × 0.2) + (Risque × 0.1)`

### Sortie

- Liste triée des projets du plus prioritaire au moins prioritaire
- Tableau de détail par projet (scores par critère + explication)
- Tableau de synthèse récapitulatif

### Ajustements possibles

Les poids peuvent être modifiés à la demande (ex. : "augmente l'urgence à 40%"). Les poids doivent toujours totaliser 100%.

---

## 2. `project-decomposer` — Décomposition en Epics / Stories / Tâches

### Objectif

Ce skill décompose un projet Linear (dont Mia est Lead) en une hiérarchie structurée d'Epics, Stories et Tâches techniques, puis crée toutes les issues directement dans Linear.

### Déclenchement

Utiliser ce skill quand l'utilisateur demande à :
- découper un projet, créer des epics, décomposer en stories
- planifier les tâches ou structurer un projet Linear
- rendre un projet existant actionnable

> **Contrainte** : s'applique uniquement aux projets dont Mia est Lead.

### Structure générée

```
EPIC — fonctionnalité majeure (2–6 semaines)
  └── STORY — unité de valeur utilisateur (1–5 jours)
        └── TÂCHE — action atomique (< 1 jour)
```

Chaque niveau inclut :
- une description claire
- une estimation (T-shirt pour les Epics, Fibonacci pour les Stories, jours pour les Tâches)
- des critères d'acceptation au format Gherkin (minimum 3 par Story)

### Labels créés automatiquement

| Label | Couleur |
|---|---|
| Epic | Violet `#8B5CF6` |
| Story | Bleu `#3B82F6` |
| Task | Vert `#10B981` |

### Workflow

1. Identification du projet de Mia dans Linear
2. Vérification / création des labels
3. Analyse des issues existantes (évite les doublons)
4. Génération de la structure complète
5. Affichage du récapitulatif et **demande de confirmation**
6. Création des issues dans Linear (Epics → Stories → Tâches)
7. Résumé final avec totaux

---

## Compatibilité

Les deux skills utilisent le MCP Linear (`https://mcp.linear.app/mcp`) et sont réservés aux projets dont **Mia est Lead**.
