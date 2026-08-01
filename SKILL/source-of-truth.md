---
name: source-of-truth
description: Maintient un notebook NotebookLM comme source de vérité (single source of truth) du projet. À utiliser systématiquement pour persister les décisions de stack et d'objectifs, les plans d'implémentation et les sources web, ainsi que pour trancher tout doute sur le contexte du projet en interrogeant le notebook.
---

# Source of Truth — Notebook de Projet

Ce skill établit le notebook NotebookLM du projet comme **source de vérité unique**. Chaque décision, plan et source pertinente doit y être persistée afin de ne jamais perdre de contexte, et tout doute sur ce que l'utilisateur a dit ou décidé se résout en interrogeant le notebook — jamais en devinant, jamais en interrompant l'utilisateur.

---

## 1. Tools callable

| Tool | Signature | Description |
|---|---|---|
| `list_notebooks` | `list_notebooks()` | Liste tous les notebooks existants (ID + titre). |
| `create_notebook` | `create_notebook(title: str)` | Crée un nouveau notebook et retourne son `notebook_id`. |
| `list_sources` | `list_sources(notebook_id: str)` | Liste les sources du notebook (ID + titre). |
| `add_source_text` | `add_source_text(notebook_id: str, text: str, title: str)` | Ajoute un document texte comme source au notebook. |
| `add_source_url` | `add_source_url(notebook_id: str, url: str)` | Ajoute une page web comme source au notebook. |
| `add_research` | `add_research(notebook_id: str, query: str)` | Lance une recherche web par IA (Fast Research) et ajoute les sources trouvées au notebook. |
| `query_notebook` | `query_notebook(notebook_id: str, question: str)` | Pose une question au notebook ; réponse générée à partir des sources avec citations. |

---

## 2. Règles d'or

1. **Ne jamais deviner ni interrompre** : dès qu'un doute survient sur ce que l'utilisateur a pu dire, vouloir ou décider, interroger le notebook avec `query_notebook` avant toute autre action.
2. **Tout choix de l'utilisateur est persisté** : stack, objectifs, contraintes, conventions — chaque décision devient un document source.
3. **Chaque plan d'implémentation est persisté** : tout plan rédigé par l'agent est ajouté comme source au notebook.
4. **Chaque source consultée en recherche web est ajoutée** : les URLs parcourues doivent être persistées via `add_source_url`, et les recherches systématiques via `add_research`.
5. **Un seul notebook par projet** : le notebook est nommé avec le nom du projet. Ne jamais en créer de doublon (toujours vérifier avec `list_notebooks` d'abord).

---

## 3. Workflow d'initialisation (Obligatoire)

Dès le début d'une session sur un projet, exécuter cette séquence :

1. Appeler `list_notebooks()`.
2. Si aucun notebook ne porte le nom du projet courant :
   - Appeler `create_notebook(title="<nom du projet>")`.
   - Noter le `notebook_id` retourné.
3. Sinon, récupérer le `notebook_id` correspondant dans la liste.
4. **Mémoriser ce `notebook_id`** et le transmettre à **tous** les appels de tools qui en dépendent (`add_source_text`, `add_source_url`, `add_research`, `query_notebook`...).
5. Si le notebook vient d'être créé, rédiger et ajouter un document de cadrage initial :
   - `add_source_text(notebook_id, text="<objectifs, stack et contexte du projet>", title="Cadrage — <nom du projet>")`.

---

## 4. Workflow de capture des décisions

Chaque fois que l'utilisateur exprime un choix (stack, architecture, objectifs, périmètre, conventions, contraintes, etc.) :

1. Rédiger un document concis et structuré qui formalise la décision (contexte, choix, justification si connue, date).
2. L'ajouter comme source :
   - `add_source_text(notebook_id, text=<contenu du document>, title="Décisions — <sujet> — <date>")`.
3. En cas de mise à jour d'une décision déjà documentée, **ne pas dupliquer** : rédiger un document qui surclasse ou précise la décision précédente (le chat du notebook synthétisera l'état le plus récent).

---

## 5. Workflow de persistance des plans d'implémentation

Chaque fois que l'agent rédige un plan d'implémentation (plan technique, étapes, taches, TODOs, choix d'implémentation) :

1. Rédiger le plan de manière autonome et autonome.
2. L'ajouter comme source au notebook :
   - `add_source_text(notebook_id, text=<plan complet>, title="Plan d'implémentation — <sujet> — <date>")`.

---

## 6. Workflow de persistance des sources web

Lors d'une recherche web ou d'une navigation pour le projet :

1. Pour une recherche web exploratoire : utiliser `add_research(notebook_id, query="<sujet de la recherche>")` afin d'ajouter automatiquement les sources trouvées.
2. Pour chaque URL individuelle consultée et jugée pertinente : `add_source_url(notebook_id, url="<url>")`.
3. Ne pas hésiter à ajouter aussi les sources que l'utilisateur fournit lui-même (liens, docs, specs).

---

## 7. Workflow de résolution des doutes (Règle absolue)

Lorsque l'agent a le moindre doute sur ce que l'utilisateur a pu dire, décider ou envisager :

1. **Interroger d'abord le notebook** :
   - `query_notebook(notebook_id, question="<question précise sur le contexte/les décisions>")`.
2. Si la réponse du notebook résout le doute : **agir en conséquence**, sans déranger l'utilisateur.
3. Si la réponse est insuffisante ou non concluante : formuler une hypothèse raisonnable, la documenter (ajout d'une source), et **ne poser une question à l'utilisateur qu'en dernier recours**, en citant ce qui a déjà été vérifié.

---

## 8. Consignes de rédaction des documents

- **Langue** : rédiger dans la langue du projet (par défaut le français).
- **Concision** : documents clairs, structurés (titres, listes), sans verbiage.
- **Datage** : inclure la date dans le titre ou en tête de chaque document.
- **Autonomie** : ne jamais bloquer le travail de l'agent en attente de confirmation pour persister un document — la persistance est un réflexe permanent.
