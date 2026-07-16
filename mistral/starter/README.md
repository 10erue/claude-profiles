# 🚀 Profil Mistral : Starter

**Équivalent Mistral** du profil `starter` de Claude Code.

---

## 📌 Description

Ce profil est une **base neutre** conçue pour :
- **Démarrer** des tâches simples ou génériques.
- **Router** vers un profil spécialisé si nécessaire (ex: `dev-saas`, `designer`, `marketing`).
- **Coordonner** plusieurs profils pour des tâches multi-expertises.

> ⚠️ **Aucune skill spécialisée** : Ce profil n'embarque **aucune compétence technique** (contrairement à `dev-saas` ou `marketing`). Il sert de point d'entrée ou de coordinateur.

---

## 🎯 Quand l'utiliser ?

| **Cas d'usage** | **Exemple** | **Profil recommandé** |
|----------------|-------------|-----------------------|
| Tâche générique | "Explique-moi ce fichier" | ✅ `starter` |
| Tâche technique | "Corrige ce bug dans le code" | ❌ Non → Utilise `dev-saas` |
| Design UI/UX | "Crée une maquette pour cette page" | ❌ Non → Utilise `designer` |
| SEO/Marketing | "Optimise ce texte pour le référencement" | ❌ Non → Utilise `marketing` |
| Coordination | "J'ai besoin de design ET de SEO" | ✅ `starter` (pour router) |

---

## 🛠️ Comment l'utiliser avec Mistral ?

### 1. **Invocation directe**
Pour activer ce profil, **fais référence à son fichier `AGENT.md`** dans ta requête :

> **Exemple** :
> "En tant qu'agent `mistral-starter`, explique-moi la structure de ce dépôt."

Ou plus simplement :
> "Utilise le profil `starter` pour analyser ce fichier."

---

### 2. **Routing vers un profil spécialisé**
Si ta tâche nécessite une expertise, `starter` te **recommandera** le profil adapté.

> **Exemple** :
> **Toi** : "Je veux corriger un bug dans mon API."
> **Mistral (starter)** : "Cette tâche relève du profil `dev-saas`. Pour l'invoquer dans Claude Code, utilise la commande :
> ```
> /dev-saas Corrige ce bug dans mon API
> ```"

---

### 3. **Coordination multi-profils**
Pour des tâches nécessitant plusieurs expertises, `starter` peut te guider pour **combiner plusieurs profils**.

> **Exemple** :
> **Toi** : "Je veux refondre ma landing page et optimiser son SEO."
> **Mistral (starter)** : "Pour cette tâche, je recommande :
> 1. **`/designer`** : Pour la refonte UI/UX.
> 2. **`/marketing`** : Pour l'optimisation SEO.
> 
> Lance les deux commandes en parallèle dans Claude Code pour comparer leurs retours."

---

## 📁 Structure du dossier

```
mistral/starter/
├── AGENT.md          # Rôle, doctrine et instructions pour Mistral
├── README.md         # Ce fichier (guide d'utilisation)
├── DOCTRINE.md       # Règles communes à tous les profils Mistral
└── EXAMPLES.md       # Exemples concrets d'utilisation
```

---

## 🔄 Différences avec le profil Claude Code

| **Fonctionnalité** | **Claude Code (`profil-starter.plugin`)** | **Mistral (`mistral/starter/`)** |
|--------------------|------------------------------------------|----------------------------------|
| **Format** | Plugin `.plugin` (archive ZIP) | Dossier avec fichiers `.md` |
| **Invocation** | Commande `/starter <tâche>` | Référence à `AGENT.md` dans la requête |
| **Skills** | Aucune (préréquis manuels) | Aucune (adapté pour Mistral) |
| **Sous-agent** | Agent dédié (`agents/starter.md`) | Instructions dans `AGENT.md` |
| **MCP** | Serveurs MCP (ex: `playwright-mcp`) | ❌ Non supporté (Mistral n'a pas accès aux MCP) |
| **Plugins** | Dépendances (ex: `frontend-design`) | ❌ Non supporté (Mistral n'a pas accès aux plugins Claude) |

---

## 📌 Préréquis (pour Claude Code)

> ⚠️ **Pour Mistral** : Ces préréquis ne s'appliquent **pas** (ils sont spécifiques à Claude Code).
> Ils sont listés ici pour référence si tu utilises aussi les profils originaux.

Le profil `starter` original nécessite les dépendances suivantes (à installer manuellement) :
- `claude-code-cli` (CLI) : `npm install -g @anthropic-ai/claude-code`
- `karpathy-claude-md` (manuel) : Copier le `CLAUDE.md` de Karpathy à la racine du projet.
- Plugins :
  - `frontend-design`
  - `claude-mem`
  - `superpowers`
  - `impeccable`
  - `ecc` (pour `chrome-devtools-mcp`)

---

## 🔗 Liens utiles

- **Dépôt original** : [10erue/claude-profiles](https://github.com/10erue/claude-profiles)
- **Autres profils Mistral** : Voir les dossiers dans `/mistral/` (ex: `dev-saas`, `designer`).
- **Documentation Claude Code** : Voir le `README.md` du dépôt pour plus de détails.

---

## 💡 Conseils

1. **Pour des tâches simples** : Utilise directement `starter` ou Mistral sans profil.
2. **Pour des tâches techniques** : Route vers le profil spécialisé (ex: `dev-saas` pour le code, `designer` pour l'UI/UX).
3. **Pour des projets complexes** : Combine plusieurs profils (ex: `designer` + `marketing` pour une landing page).
4. **Pour explorer** : Commence par `starter` pour comprendre la structure du dépôt avant de choisir un profil.
