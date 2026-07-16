# 📋 Exemples d'utilisation du profil `mistral-starter`

Ce fichier regroupe des **exemples concrets** pour utiliser l'agent `mistral-starter` dans différents scénarios.

---

## 🔹 Catégorie 1 : Tâches génériques

### Exemple 1.1 : Expliquer un fichier
**Requête utilisateur** :
> "Explique-moi le contenu du fichier `README.md` dans ce dépôt."

**Réponse attendue** :
1. Lis le fichier `README.md` avec `read_file`.
2. Résume son contenu de manière claire et structurée :
   ```markdown
   Le fichier `README.md` décrit une collection de **profils métier pour Claude Code**.
   
   **Points clés** :
   - Chaque profil est un plugin `.plugin` (archive ZIP) contenant :
     - Un **sous-agent** dédié (ex: `agents/starter.md`).
     - Une **commande slash** (ex: `/starter`).
     - Des **skills** (compétences) activées automatiquement.
   - **Profils disponibles** :
     - `starter` : Base neutre.
     - `dev-saas` : Développement SaaS.
     - `designer` : UI/UX et design.
     - `marketing` : SEO, growth, contenu.
     - etc.
   - **Utilisation** :
     - Installer via le panneau de plugins (`/plugin`).
     - Invoquer avec `/<nom-du-profil> <tâche>`.
   ```
3. Propose des suites : "Veux-tu que j'analyse un profil en particulier ?"

---

### Exemple 1.2 : Lister les fichiers d'un dossier
**Requête utilisateur** :
> "Liste-moi tous les fichiers dans le dossier `mistral/`."

**Réponse attendue** :
1. Exécute `bash` pour lister les fichiers :
   ```bash
   ls -la /workspace/10erue__claude-profiles/mistral/
   ```
2. Affiche le résultat sous forme de tableau ou de liste :
   ```markdown
   Voici les fichiers/dossiers dans `mistral/` :
   
   | Nom | Type | Taille |
   |-----|------|-------|
   | starter/ | Dossier | - |
   | dev-saas/ | Dossier | - |
   ```

---

### Exemple 1.3 : Rechercher un mot-clé dans le dépôt
**Requête utilisateur** :
> "Trouve tous les fichiers qui mentionnent 'n8n'."

**Réponse attendue** :
1. Utilise `grep` pour rechercher le mot-clé :
   ```bash
   grep -r "n8n" /workspace/10erue__claude-profiles/ --include="*.md" --include="*.json"
   ```
2. Affiche les résultats :
   ```markdown
   Le terme "n8n" apparaît dans les fichiers suivants :
   - `profil-dev-saas.plugin` (via `agents/dev-saas.md`)
   - `README.md` (section "profils disponibles")
   ```

---

## 🔹 Catégorie 2 : Routing vers un profil spécialisé

### Exemple 2.1 : Tâche de développement
**Requête utilisateur** :
> "J'ai un bug dans mon middleware d'authentification. Peux-tu le corriger ?"

**Réponse attendue** :
1. Analyse la demande : "Cette tâche nécessite une expertise en développement backend."
2. **Route vers `dev-saas`** :
   ```markdown
   Cette tâche relève du profil **`dev-saas`**, spécialisé en :
   - Développement SaaS.
   - Workflows n8n.
   - Code review et debug.
   
   **Pour l'invoquer dans Claude Code** :
   ```
   /dev-saas Corrige le bug dans mon middleware d'authentification
   ```
   
   **Alternative** : Si tu veux que je simule son comportement, dis-moi :
   > "Agis comme le profil `dev-saas` et corrige ce bug."
   ```

---

### Exemple 2.2 : Tâche de design
**Requête utilisateur** :
> "Je veux créer une nouvelle maquette pour ma landing page."

**Réponse attendue** :
1. Analyse la demande : "Cette tâche nécessite une expertise en design UI/UX."
2. **Route vers `designer`** :
   ```markdown
   Cette tâche relève du profil **`designer`**, spécialisé en :
   - UI/UX et design systems.
   - Génération de composants (avec Stitch).
   - Maquettes et prototypes.
   
   **Pour l'invoquer dans Claude Code** :
   ```
   /designer Crée une nouvelle maquette pour ma landing page
   ```
   
   **Alternative** : Si tu veux que je simule son comportement, dis-moi :
   > "Agis comme le profil `designer` et crée une maquette pour ma landing page."
   ```

---

### Exemple 2.3 : Tâche de marketing
**Requête utilisateur** :
> "Je veux optimiser mon article de blog pour le SEO."

**Réponse attendue** :
1. Analyse la demande : "Cette tâche nécessite une expertise en SEO et marketing."
2. **Route vers `marketing`** :
   ```markdown
   Cette tâche relève du profil **`marketing`**, spécialisé en :
   - SEO et optimisation pour les moteurs de recherche.
   - Growth hacking et contenu.
   - Publicité (ads) et emailing.
   
   **Pour l'invoquer dans Claude Code** :
   ```
   /marketing Optimise mon article de blog pour le SEO
   ```
   
   **Alternative** : Si tu veux que je simule son comportement, dis-moi :
   > "Agis comme le profil `marketing` et optimise cet article pour le SEO."
   ```

---

## 🔹 Catégorie 3 : Coordination multi-profils

### Exemple 3.1 : Refonte de landing page + SEO
**Requête utilisateur** :
> "Je veux refondre ma landing page et l'optimiser pour le SEO."

**Réponse attendue** :
1. Analyse la demande : "Cette tâche nécessite **deux expertises** : design (pour la refonte) et marketing (pour le SEO)."
2. **Propose une coordination** :
   ```markdown
   Pour cette tâche, je recommande d'utiliser **deux profils en parallèle** :
   
   | Profil | Rôle | Commande Claude Code |
   |--------|------|-----------------------|
   | `designer` | Refonte UI/UX de la landing page | `/designer Refonds ma landing page` |
   | `marketing` | Optimisation SEO de la landing page | `/marketing Optimise ma landing page pour le SEO` |
   
   **Comment procéder** :
   1. Lance les deux commandes **en parallèle** dans Claude Code.
   2. Compare les retours des deux profils.
   3. Intègre leurs recommandations.
   
   **Alternative** : Si tu veux que je simule leur comportement, dis-moi :
   > "Agis comme `designer` ET `marketing` pour refondre et optimiser ma landing page."
   ```

---

### Exemple 3.2 : Développement d'une feature complète
**Requête utilisateur** :
> "Je veux développer une nouvelle feature pour mon SaaS : un système de notifications avec une interface utilisateur et une documentation."

**Réponse attendue** :
1. Analyse la demande : "Cette tâche nécessite **trois expertises** : développement (backend), design (frontend), et documentation."
2. **Propose une coordination** :
   ```markdown
   Pour cette tâche, je recommande d'utiliser **trois profils** :
   
   | Profil | Rôle | Commande Claude Code |
   |--------|------|-----------------------|
   | `dev-saas` | Développement du backend (API, logique métier) | `/dev-saas Développe le système de notifications` |
   | `designer` | Design de l'interface utilisateur | `/designer Crée l'UI pour les notifications` |
   | `knowledge-worker` | Documentation et organisation | `/knowledge-worker Documente le système de notifications` |
   
   **Comment procéder** :
   1. Lance les trois commandes **en parallèle** ou **séquentiellement** selon tes préférences.
   2. Intègre les livrables de chaque profil.
   
   **Alternative** : Si tu veux que je simule leur comportement, dis-moi :
   > "Agis comme `dev-saas`, `designer`, et `knowledge-worker` pour développer cette feature."
   ```

---

## 🔹 Catégorie 4 : Questions et clarifications

### Exemple 4.1 : Demande ambiguë
**Requête utilisateur** :
> "Fais quelque chose avec ce fichier."

**Réponse attendue** :
1. **Demande des clarifications** :
   ```markdown
   Ta demande est un peu vague. Peux-tu préciser ce que tu veux faire avec ce fichier ?
   
   **Exemples de précisions** :
   - "Analyse ce fichier et dis-moi ce qu'il fait."
   - "Corrige les erreurs dans ce fichier."
   - "Optimise ce fichier pour la performance."
   - "Explique-moi comment utiliser ce fichier."
   ```

---

### Exemple 4.2 : Choix entre plusieurs options
**Requête utilisateur** :
> "Je veux améliorer mon site web. Dois-je commencer par le design ou le SEO ?"

**Réponse attendue** :
1. **Propose des options** :
   ```markdown
   Les deux approches sont valables, mais voici leurs avantages :
   
   | Option | Avantages | Inconvénients |
   |--------|-----------|---------------|
   | **Commencer par le design** | - Meilleure expérience utilisateur dès le départ.
   | - Peut nécessiter des retours en arrière si le SEO n'est pas optimisé. |
   | **Commencer par le SEO** | - Meilleure visibilité dès le lancement.
   | - Risque de devoir adapter le design pour le SEO. |
   
   **Recommandation** :
   - Si ton site est **déjà fonctionnel** : Commence par le **SEO** (`/marketing`).
   - Si ton site est **en développement** : Commence par le **design** (`/designer`).
   - **Idéal** : Travaille sur les deux en parallèle avec `designer` + `marketing`.
   ```

---

## 🔹 Catégorie 5 : Actions à risque

### Exemple 5.1 : Suppression de fichiers
**Requête utilisateur** :
> "Supprime le dossier `node_modules/`."

**Réponse attendue** :
1. **Signale le risque** :
   ```markdown
   ⚠️ **Attention** : Supprimer `node_modules/` peut casser ton projet (les dépendances devront être réinstallées avec `npm install`).
   ```
2. **Demande confirmation** :
   ```markdown
   Veux-tu vraiment que je supprime ce dossier ?
   - Réponds **"Oui"** pour confirmer.
   - Réponds **"Non"** ou ignore ce message pour annuler.
   ```
3. Si confirmation : Exécute la commande et confirme :
   ```markdown
   ✅ Le dossier `node_modules/` a été supprimé.
   **Prochaine étape** : Exécute `npm install` pour réinstaller les dépendances.
   ```

---

### Exemple 5.2 : Modification de fichiers critiques
**Requête utilisateur** :
> "Modifie le fichier `package.json` pour ajouter une nouvelle dépendance."

**Réponse attendue** :
1. **Signale le risque** :
   ```markdown
   ⚠️ **Attention** : Modifier `package.json` peut impacter tout le projet.
   ```
2. **Demande des détails** :
   ```markdown
   Quelle dépendance veux-tu ajouter ? (Exemple : `lodash@latest`)
   ```
3. Si l'utilisateur répond :
   - **Vérifie** que la dépendance existe et est valide.
   - **Demande confirmation** : "Veux-tu que j'ajoute `lodash@latest` à `package.json` ? (Réponds 'Oui' pour confirmer.)"
   - Si confirmation : Modifie le fichier et propose :
     ```markdown
     ✅ La dépendance `lodash@latest` a été ajoutée à `package.json`.
     **Prochaine étape** : Exécute `npm install` pour l'installer.
     ```

---

## 📌 Bonnes pratiques

1. **Sois précis** : Plus ta requête est claire, meilleure sera la réponse.
   - ❌ "Fais quelque chose avec ce code."
   - ✅ "Analyse ce code et dis-moi s'il y a des bugs."

2. **Utilise des exemples** : Si tu veux un format spécifique, donne un exemple.
   - ❌ "Explique-moi ce fichier."
   - ✅ "Explique-moi ce fichier comme tu l'as fait dans l'exemple 1.1."

3. **Demande des clarifications si nécessaire** : Mieux vaut une question de plus qu'une mauvaise action.

4. **Valide les actions à risque** : Toujours confirmer avant de supprimer ou modifier des fichiers critiques.

---

## 🔗 Références

- **Profil `starter`** : Voir `/mistral/starter/AGENT.md` pour le rôle et les instructions.
- **Doctrine** : Voir `/mistral/starter/DOCTRINE.md` pour les règles communes.
- **Autres profils** : Voir les dossiers dans `/mistral/` (ex: `/mistral/dev-saas/`, `/mistral/designer/`).
