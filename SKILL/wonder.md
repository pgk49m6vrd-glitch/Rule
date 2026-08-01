---
name: wonder-prompting
description: Guidage avancé pour l'utilisation du MCP Wonder. À utiliser dès que l'utilisateur souhaite créer, modifier, réorganiser ou concevoir des interfaces sur le canvas Wonder via le MCP Wonder.
---

# Wonder MCP Prompting & Design Engineering Skill

Ce skill définit la doctrine pour interagir efficacement avec **Wonder MCP**, l'outil de création et modification de design UI basé sur canvas (React + Tailwind IR).

---

## 2. Phase Initiale & Ingestion du Contexte (Obligatoire)

Avant toute action de génération ou de modification de design, l'agent doit suivre cette séquence d'initialisation :

1. **Obtenir le `pageId` à jour** :
   - Exécuter systématiquement `get_basic_info` pour récupérer le `pageId`, le fichier actif, la branche et les icônes disponibles.
   - ⚠️ **Règle absolue** : Transmettre ce `pageId` à **tous** les appels de tools dépendant de la page (`create_artboard`, `get_element_tree`, `update_elements`, `duplicate_elements`, `take_screenshot`, etc.).

2. **Charger le Behavior Skill adapté** :
   - Appeler `get_behavior_skills` pour récupérer les instructions pas-à-pas correspondant à la tâche :
     - **Création complète ex-nihilo** : `create-design`
     - **Modification majeure / Restylage** : `update-design`
     - **Assemblage à partir d'une bibliothèque** : `assemble-screen-from-components`
     - **Import de bibliothèque de code** : `import-component-library`
     - **Gestion & extraction de variables/tokens** : `manage-design-tokens`
     - **Revue visuelle post-génération** : `review-design`

---

## 4. Formats & Directives IR (Intermediate Representation) Stricts

Lorsque l'agent formule du code JSX / IR pour Wonder :

- **Balises autorisées uniquement** : `div`, `span`, `img`, `svg`. (Pas de `<button>`, `<header>`, `<h1>`, `<p>`, `<a>`).
- **Attribut Label Requis** : Tout `div` et `span` doit avoir un `data-node-label="..."` en Title Case.
- **Gestion des IDs** : Ne jamais inventer de `data-node-id` sur du neuf ; préserver strictly les IDs existants lors d'un `update_elements`.
- **Liaison des Tokens CSS** :
  - Format identifiant : `var(--brand-primary)` (jamais de `/` dans le nom de la variable CSS).
  - Couleurs / Tailles : `bg-[var(--brand-primary)]`, `p-[var(--space-md)]`.
  - Polices avec Hint obligatoire : `font-[family-name:var(--font-serif)]` et `font-[number:var(--weight-bold)]`.
- **Icônes** : Utiliser en priorité les icônes du catalogue du fichier (`get_basic_info`), puis `react-icons`, puis SVG inline.

---

## 5. Stratégie de Duplication (Preserve the Original)

- **Modifications substantielles** (Restylage complet, refonte de layout, réécriture de section) :
  - Dupliquer d'abord l'artboard avec `duplicate_elements(newArtboard=true)`.
  - Appliquer les modifications sur la **COPIE** en ciblant les nouveaux `data-node-id` générés.
- **Ajustements mineurs (Fast-Path)** (Changement de texte simple, swap de couleur ou d'icône) :
  - Modifier directement sur l'élément original sans dupliquer.
- **Modification In-Place Explicite** : Ne modifier l'original en place que si l'utilisateur le demande expressément ("change l'original", "édite sur place").

---

## 6. Workflow de Finalisation Stricte

À la fin de la séquence d'édition :
1. Effectuer une vérification visuelle finale avec `take_screenshot`.
2. Si c'est une nouvelle page complète, charger `review-design` pour vérifier la grille d'évaluation UI.
3. Appeler `finish_artboard(artboardId=...)` pour chaque artboard modifié.
4. Appeler **exclusivement une seule fois** `finish_session()` en tout dernier outil pour sauvegarder la session sur le canvas.
