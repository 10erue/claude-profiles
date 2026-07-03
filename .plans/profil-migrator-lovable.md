# profil-migrator-lovable — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a reusable Claude Code plugin `profil-migrator-lovable` — a sub-agent + skill set that migrates any Lovable-style mocked React prototype into the "station END" paradigm (feature-based, TanStack Router, mocked apiClient façade, END i18n, shadcn/ui), producing a functional, unit-tested, gate-verified front.

**Architecture:** A dedicated orchestrator sub-agent (`migrator-lovable`) invokes 5 focused skills in sequence: analyze-source → extract-target-paradigm → build-correspondence-map → migrate-screen → verify-migration. The target paradigm is **hybrid**: a versioned contract doc (`standards/paradigm-END.md`) that the agent reads, plus a runtime re-verification skill that adapts the contract to the actual target repo. The plugin mirrors the existing `profil-*.plugin` layout in this repo (a zip archive with `.claude-plugin/plugin.json`, `agents/`, `commands/`, `skills/`, `standards/`, `README.md`).

**Tech Stack:** Markdown skill/agent authoring (Claude Code plugin format), graphify (knowledge-graph cartography of source & target), target repos are React + TypeScript + Vite + TanStack Router + Vitest.

## Global Constraints

- Plugin name: `profil-migrator-lovable` (exact, used in `plugin.json`, agent name, command name).
- Author frontmatter matches existing plugins: `"author": { "name": "JB" }`, `"version"` starts at `0.1.0`.
- The agent NEVER invokes the Agent/Task tool to spawn other sub-agents (it is itself a sub-agent — avoid nested loops). This rule is copied from `profil-starter` agents/starter.md step 6.
- Migration scope enforced by the agent: **walking-skeleton first** (one screen end-to-end) before generalizing; **mocked data only** behind an apiClient-shaped façade (no real API wiring); adopt END conventions integrally (feature-based `src/features/*`, TanStack file-routes, END i18n, shadcn/ui + `cn()`).
- Test bar for a migrated front: **unit tests (Vitest) + typecheck + lint + build all green**. No E2E required.
- All skill files use YAML frontmatter with `name` and `description` keys (per Claude Code skill spec). Agent files use `name`, `description`, `model: inherit`, `color`.
- Golden validation case: source = `/Users/rudybazantay/Documents/projects/seekube/prototype`, target paradigm = `/Users/rudybazantay/Documents/projects/seekube/seekube-v3/apps/web`.
- Dev layout: build the plugin as an **unpacked directory** `profil-migrator-lovable/` at repo root; packaging to a `.plugin` zip is the final task.
- French is the working language for user-facing docs (README, command description), matching sibling plugins.

---

### Task 1: Scaffold the plugin skeleton

**Files:**
- Create: `profil-migrator-lovable/.claude-plugin/plugin.json`
- Create: `profil-migrator-lovable/README.md`
- Create: `profil-migrator-lovable/CHANGELOG.md`

**Interfaces:**
- Consumes: nothing (first task).
- Produces: the plugin root directory `profil-migrator-lovable/` recognized as a Claude Code plugin via a valid `plugin.json`.

- [ ] **Step 1: Create `plugin.json`**

```json
{
  "name": "profil-migrator-lovable",
  "version": "0.1.0",
  "description": "Agent + skills Claude Code pour migrer un prototype Lovable (React mocké) vers le paradigme cible 'station END' (feature-based, TanStack Router, façade apiClient mockée, i18n END, shadcn/ui), avec front fonctionnel testé et vérifié.",
  "author": {
    "name": "JB"
  }
}
```

- [ ] **Step 2: Create `README.md`** (French, mirrors sibling plugins' tone)

```markdown
# profil-migrator-lovable

Plugin Claude Code : un sous-agent orchestrateur et son jeu de skills pour **migrer un prototype Lovable** (React mocké, structure plate) vers le **paradigme cible "station END"** — architecture feature-based, TanStack Router (file-routes), façade `apiClient` mockée, i18n END, primitives shadcn/ui + `cn()`.

Objectif d'une migration : un front **fonctionnel, testé (Vitest) et vérifié** (typecheck + lint + build verts). Les données restent **mockées** derrière une façade à la signature `apiClient`, pour brancher la vraie API plus tard sans refacto.

## Usage

`/migrator-lovable <chemin-proto-source> --target <chemin-repo-END>`

Le sous-agent enchaîne : analyse source → extraction/adaptation du paradigme cible → table de correspondance → migration walking-skeleton d'un écran → vérification (gates verts) → généralisation écran par écran.

## Skills embarquées

1. `analyze-source` — cartographie le proto (graphify) : écrans, composants, données mock, routing, i18n, primitives UI.
2. `extract-target-paradigm` — lit `standards/paradigm-END.md` et le ré-vérifie contre le repo cible fourni (hybride).
3. `build-correspondence-map` — produit la table de correspondance start→end + flags des cas non mappables.
4. `migrate-screen` — migre un écran de bout en bout selon la table (feature folder, route, façade mock, i18n, UI), test unitaire d'abord.
5. `verify-migration` — lance les gates (typecheck, lint, build, Vitest) et boucle jusqu'au vert.

## Doctrine cible

Voir `standards/paradigm-END.md` — contrat versionné du paradigme END.
```

- [ ] **Step 3: Create `CHANGELOG.md`**

```markdown
# Changelog

## 0.1.0
- Version initiale : agent `migrator-lovable` + 5 skills + doctrine paradigme END.
```

- [ ] **Step 4: Verify `plugin.json` is valid JSON**

Run: `python3 -c "import json; json.load(open('profil-migrator-lovable/.claude-plugin/plugin.json')); print('OK')"`
Expected: `OK`

- [ ] **Step 5: Commit**

```bash
git add profil-migrator-lovable/.claude-plugin/plugin.json profil-migrator-lovable/README.md profil-migrator-lovable/CHANGELOG.md
git commit -m "feat(migrator-lovable): scaffold plugin skeleton"
```

---

### Task 2: Author the target-paradigm doctrine (`standards/paradigm-END.md`)

**Files:**
- Create: `profil-migrator-lovable/standards/paradigm-END.md`

**Interfaces:**
- Consumes: END conventions discovered from the golden target repo (`seekube-v3/apps/web`) — via graphify graph already built at its `graphify-out/graph.json`, plus direct file reads.
- Produces: the versioned contract doc that `extract-target-paradigm` reads. Every section below is a REQUIRED FIELD the doc must fill with concrete, copy-pasteable values.

- [ ] **Step 1: Extract the concrete END conventions from the golden target**

For each required field, read the real source in `seekube-v3/apps/web` (do not invent). Capture exact values:
- **Package manager & scripts**: read `apps/web/package.json` → exact commands for `test`, `typecheck` (or `tsc`), `lint`, `build`. Detect PM from lockfile (`pnpm-lock.yaml` / `package-lock.json` / `yarn.lock`).
- **Path aliases**: read `tsconfig.json` + `vite.config.*` → e.g. `@/` → `src/`.
- **Feature layout**: pick one real folder under `src/features/`, record its file set and each file's role.
- **Data façade**: locate `apiClient`, record its file path + exact exported signature, and one real hook/query usage snippet (identify: TanStack Query? RTK Query? fetch wrapper?).
- **Routing**: TanStack Router — record route files dir, the exact format of one real route file (`createFileRoute`, loader), and how a new route is added.
- **i18n**: record the library, where strings live, and the exact translate hook/function with a real snippet.
- **UI primitives**: exact path of `cn()` and the shadcn `ui/` dir.

- [ ] **Step 2: Write `standards/paradigm-END.md`** with this exact structure, each section filled with the values from Step 1:

```markdown
# Paradigme cible « station END » — contrat de migration

> Contrat versionné lu par la skill `extract-target-paradigm`. Valeurs par défaut = repo golden `seekube-v3/apps/web`. La skill ré-vérifie chaque champ contre le repo cible réel au runtime et signale toute divergence.

## 1. Outillage
- Gestionnaire de paquets : <pnpm|npm|yarn>
- Commandes gates : test=`<cmd>`, typecheck=`<cmd>`, lint=`<cmd>`, build=`<cmd>`
- Alias d'import : `<@/ -> src/>`

## 2. Architecture feature-based
- Un écran/domaine = un dossier `src/features/<feature>/`
- Fichiers types et rôles : <liste réelle issue d'un feature exemple>

## 3. Façade données (apiClient)
- Fichier : `<path>`
- Signature exportée : `<signature>`
- Pattern d'appel (hook/query) : <snippet réel>
- Règle migration : mock servi derrière la MÊME signature (pas de fetch inline)

## 4. Routing (TanStack Router)
- Dossier des routes : `<path>`
- Format d'une route : <snippet réel createFileRoute>
- Ajout d'une route : <procédure>

## 5. i18n
- Librairie : `<lib>`
- Emplacement des chaînes : `<path>`
- Hook/fonction : <snippet réel>

## 6. Primitives UI
- `cn()` : `<path>`
- Composants shadcn `ui/` : `<dir>`

## 7. Barre de qualité d'une migration
- Vitest vert + typecheck + lint + build verts. Pas d'E2E requis.
```

- [ ] **Step 3: Verify the doc has no unfilled placeholders**

Run: `grep -nE '<[a-z]|TODO|TBD' profil-migrator-lovable/standards/paradigm-END.md && echo "STILL HAS PLACEHOLDERS" || echo "NO PLACEHOLDERS"`
Expected: `NO PLACEHOLDERS`

- [ ] **Step 4: Commit**

```bash
git add profil-migrator-lovable/standards/paradigm-END.md
git commit -m "feat(migrator-lovable): add END target paradigm doctrine"
```

---

### Task 3: Author skill `analyze-source`

**Files:**
- Create: `profil-migrator-lovable/skills/analyze-source/SKILL.md`

**Interfaces:**
- Consumes: a source proto path (from the agent).
- Produces: writes a source inventory to `<source>/graphify-out/SOURCE_INVENTORY.md` with sections: Screens, Components, MockData, Routing, i18nUsage, UIPrimitives. Downstream skill `build-correspondence-map` reads this exact file.

- [ ] **Step 1: Write `analyze-source/SKILL.md`**

````markdown
---
name: analyze-source
description: Cartographie un prototype Lovable (React mocké) et produit un inventaire structuré des écrans, composants, données mock, routing, usages i18n et primitives UI. Première étape d'une migration profil-migrator-lovable.
---

## analyze-source

Objectif : produire `SOURCE_INVENTORY.md` — l'état des lieux du proto source, base de la table de correspondance.

### Étapes

1. Lance graphify sur le proto : `/graphify <source>` (ou réutilise `<source>/graphify-out/graph.json` s'il existe et est à jour).
2. À partir du graphe + lecture ciblée, remplis chaque rubrique :
   - **Screens** : chaque page/vue (fichier + route actuelle).
   - **Components** : composants partagés vs spécifiques écran ; note ceux qui appellent `cn()` (portables tels quels).
   - **MockData** : où vivent les données mock (fichiers, hooks, inline) et leur forme.
   - **Routing** : librairie actuelle (react-router ?) et carte des routes.
   - **i18nUsage** : hook custom (`useLanguage()`/`t()` ?) et clés utilisées.
   - **UIPrimitives** : primitives shadcn présentes et emplacement de `cn()`.
3. Écris le résultat dans `<source>/graphify-out/SOURCE_INVENTORY.md` avec exactement ces 6 titres de niveau 2 : `## Screens`, `## Components`, `## MockData`, `## Routing`, `## i18nUsage`, `## UIPrimitives`.

### Sortie

Chemin : `<source>/graphify-out/SOURCE_INVENTORY.md`. Ne renvoie qu'un résumé (compte par rubrique) + le chemin.

### Règles token
- Réutilise le graphe existant si présent ; ne relance graphify que si le proto a changé.
````

- [ ] **Step 2: Verify frontmatter parses**

Run: `python3 -c "import re; t=open('profil-migrator-lovable/skills/analyze-source/SKILL.md').read(); m=re.match(r'^---\n(.*?)\n---', t, re.S); assert m and 'name:' in m.group(1) and 'description:' in m.group(1); print('FRONTMATTER OK')"`
Expected: `FRONTMATTER OK`

- [ ] **Step 3: Commit**

```bash
git add profil-migrator-lovable/skills/analyze-source/SKILL.md
git commit -m "feat(migrator-lovable): add analyze-source skill"
```

---

### Task 4: Author skill `extract-target-paradigm`

**Files:**
- Create: `profil-migrator-lovable/skills/extract-target-paradigm/SKILL.md`

**Interfaces:**
- Consumes: `standards/paradigm-END.md` (contract) + a target repo path (from the agent).
- Produces: writes a resolved, repo-adapted paradigm to `<source>/graphify-out/TARGET_PARADIGM.resolved.md` (same 7 sections as the contract, values confirmed/overridden against the real target). Downstream `build-correspondence-map` and `migrate-screen` read this file.

- [ ] **Step 1: Write `extract-target-paradigm/SKILL.md`**

````markdown
---
name: extract-target-paradigm
description: Lit le contrat de paradigme END versionné et le ré-vérifie contre le repo cible réel fourni au runtime (hybride), produisant un paradigme résolu et adapté. Deuxième étape d'une migration profil-migrator-lovable.
---

## extract-target-paradigm

Objectif : partir du contrat `standards/paradigm-END.md`, confirmer/adapter chaque champ contre le repo cible réel, et écrire le paradigme résolu.

### Étapes

1. Lis `${CLAUDE_PLUGIN_ROOT}/standards/paradigm-END.md` (les valeurs par défaut).
2. Pour chaque section (Outillage, Feature-based, Façade données, Routing, i18n, Primitives UI, Barre qualité), VÉRIFIE contre le repo cible fourni :
   - Outillage : relis `package.json` + lockfile du cible → commandes réelles.
   - Alias : relis `tsconfig.json`/`vite.config`.
   - Façade `apiClient`, feature layout, format de route TanStack, i18n, `cn()` : relis les fichiers réels ; réutilise `graphify-out/graph.json` du cible s'il existe.
3. Quand le cible diverge du contrat, PRÉFÈRE la valeur réelle du cible et note la divergence.
4. Écris `<source>/graphify-out/TARGET_PARADIGM.resolved.md` avec les 7 mêmes titres que le contrat, valeurs résolues, + une rubrique finale `## Divergences` listant tout écart contrat↔cible.

### Sortie

Chemin : `<source>/graphify-out/TARGET_PARADIGM.resolved.md`. Renvoie un résumé + la liste des divergences.
````

- [ ] **Step 2: Verify frontmatter parses**

Run: `python3 -c "import re; t=open('profil-migrator-lovable/skills/extract-target-paradigm/SKILL.md').read(); m=re.match(r'^---\n(.*?)\n---', t, re.S); assert m and 'name:' in m.group(1) and 'description:' in m.group(1); print('FRONTMATTER OK')"`
Expected: `FRONTMATTER OK`

- [ ] **Step 3: Commit**

```bash
git add profil-migrator-lovable/skills/extract-target-paradigm/SKILL.md
git commit -m "feat(migrator-lovable): add extract-target-paradigm skill"
```

---

### Task 5: Author skill `build-correspondence-map`

**Files:**
- Create: `profil-migrator-lovable/skills/build-correspondence-map/SKILL.md`

**Interfaces:**
- Consumes: `SOURCE_INVENTORY.md` (Task 3 output) + `TARGET_PARADIGM.resolved.md` (Task 4 output).
- Produces: writes `<source>/graphify-out/CORRESPONDENCE_MAP.md` with a mapping table and an `## Unmapped` section. Downstream `migrate-screen` reads this file.

- [ ] **Step 1: Write `build-correspondence-map/SKILL.md`**

````markdown
---
name: build-correspondence-map
description: Croise l'inventaire source et le paradigme cible résolu pour produire la table de correspondance start->end (le pont) et signaler les cas non mappables. Troisième étape d'une migration profil-migrator-lovable.
---

## build-correspondence-map

Objectif : produire le « pont » — règles de transformation start->end, exploitables écran par écran.

### Entrées
- `<source>/graphify-out/SOURCE_INVENTORY.md`
- `<source>/graphify-out/TARGET_PARADIGM.resolved.md`

### Étapes

1. Lis les deux entrées.
2. Construis une table Markdown avec colonnes : `Élément source | Emplacement source | Cible (paradigme END) | Emplacement cible | Règle de transformation`. Couvre au minimum :
   - Composant plat -> `src/features/<feature>/` (feature-based).
   - Données mock (inline/hook) -> façade à la signature `apiClient` (mock derrière la même API).
   - Routing source -> route TanStack (file-route) selon le format cible.
   - `useLanguage()`/`t()` source -> système i18n END.
   - Primitives shadcn + `cn()` -> alignement sur les chemins cible.
3. Sépare une rubrique `## Unmapped` : tout élément source sans équivalent cible (hacks Lovable, libs absentes) -> à flaguer pour décision humaine, jamais improviser.
4. Écris `<source>/graphify-out/CORRESPONDENCE_MAP.md`.

### Sortie
Chemin : `<source>/graphify-out/CORRESPONDENCE_MAP.md`. Renvoie un résumé + le nombre d'éléments unmapped.
````

- [ ] **Step 2: Verify frontmatter parses**

Run: `python3 -c "import re; t=open('profil-migrator-lovable/skills/build-correspondence-map/SKILL.md').read(); m=re.match(r'^---\n(.*?)\n---', t, re.S); assert m and 'name:' in m.group(1) and 'description:' in m.group(1); print('FRONTMATTER OK')"`
Expected: `FRONTMATTER OK`

- [ ] **Step 3: Commit**

```bash
git add profil-migrator-lovable/skills/build-correspondence-map/SKILL.md
git commit -m "feat(migrator-lovable): add build-correspondence-map skill"
```

---

### Task 6: Author skill `migrate-screen`

**Files:**
- Create: `profil-migrator-lovable/skills/migrate-screen/SKILL.md`

**Interfaces:**
- Consumes: `CORRESPONDENCE_MAP.md` (Task 5) + `TARGET_PARADIGM.resolved.md` (Task 4) + a chosen screen name.
- Produces: migrated files inside the target repo's `src/features/<feature>/` + a route file + a mock façade + a Vitest test file. Enforces walking-skeleton (first screen only) then generalizes.

- [ ] **Step 1: Write `migrate-screen/SKILL.md`**

````markdown
---
name: migrate-screen
description: Migre un écran du proto vers le paradigme END selon la table de correspondance — dossier feature, route TanStack, façade mock apiClient, i18n, UI shadcn — en écrivant d'abord un test unitaire. Quatrième étape d'une migration profil-migrator-lovable.
---

## migrate-screen

Objectif : migrer UN écran de bout en bout, testé. Le PREMIER écran est un walking-skeleton (choisir un écran touchant données + routing + i18n, pas un écran UI statique) : il sert de gabarit avant de généraliser.

### Entrées
- `<source>/graphify-out/CORRESPONDENCE_MAP.md`
- `<source>/graphify-out/TARGET_PARADIGM.resolved.md`
- Nom de l'écran à migrer.

### Étapes (TDD-first)

1. Crée le dossier feature `src/features/<feature>/` dans le repo cible selon le layout du paradigme résolu.
2. **Écris d'abord le test unitaire Vitest** de l'écran (rendu + comportement mock attendu) — il doit échouer.
3. Crée la **façade de données** : un module exposant la MÊME signature qu'`apiClient` mais renvoyant les données mock du proto (pas de fetch inline).
4. Porte le composant écran dans la feature, branché sur la façade ; remplace `useLanguage()`/`t()` par le système i18n cible ; aligne les primitives UI sur shadcn/`cn()` du cible.
5. Ajoute la **route TanStack** (file-route) au format du paradigme résolu.
6. Fais passer le test (implémentation minimale d'abord).
7. Pour les éléments listés `Unmapped` : NE PAS improviser — signaler et demander décision.

### Règle de périmètre
- Écran 1 = skeleton, validé (verify-migration) AVANT de migrer les suivants. Ensuite, un écran à la fois, chacun re-vérifié.

### Sortie
Liste des fichiers créés/modifiés + l'écran migré. Renvoie la main à `verify-migration`.
````

- [ ] **Step 2: Verify frontmatter parses**

Run: `python3 -c "import re; t=open('profil-migrator-lovable/skills/migrate-screen/SKILL.md').read(); m=re.match(r'^---\n(.*?)\n---', t, re.S); assert m and 'name:' in m.group(1) and 'description:' in m.group(1); print('FRONTMATTER OK')"`
Expected: `FRONTMATTER OK`

- [ ] **Step 3: Commit**

```bash
git add profil-migrator-lovable/skills/migrate-screen/SKILL.md
git commit -m "feat(migrator-lovable): add migrate-screen skill"
```

---

### Task 7: Author skill `verify-migration`

**Files:**
- Create: `profil-migrator-lovable/skills/verify-migration/SKILL.md`

**Interfaces:**
- Consumes: the resolved paradigm's gate commands (`TARGET_PARADIGM.resolved.md` → Outillage) + the target repo path.
- Produces: a pass/fail verification report; loops back to `migrate-screen` on failure.

- [ ] **Step 1: Write `verify-migration/SKILL.md`**

````markdown
---
name: verify-migration
description: Lance les gates de qualité (typecheck, lint, build, tests Vitest) sur le repo cible après migration d'un écran et boucle jusqu'au vert. Dernière étape d'une migration profil-migrator-lovable.
---

## verify-migration

Objectif : prouver que l'écran migré est fonctionnel — gates verts. Pas de claim de succès sans sortie de commande à l'appui.

### Entrées
- Commandes gates issues de `TARGET_PARADIGM.resolved.md` (section Outillage) : test, typecheck, lint, build.
- Chemin du repo cible.

### Étapes

1. Lance, dans l'ordre, sur le repo cible : `typecheck`, `lint`, `build`, `test` (Vitest, ciblé sur la feature migrée puis suite complète).
2. Si une gate échoue : lis la sortie, diagnostique, corrige (retour à `migrate-screen` si besoin), relance. Boucle jusqu'au vert.
3. Ne JAMAIS déclarer « migré/vérifié » sans les 4 gates vertes. Rapporte les sorties réelles.

### Sortie
Rapport : par gate, commande + statut (PASS/FAIL) + extrait de sortie. Vert global = écran validé (skeleton -> autorise la généralisation).
````

- [ ] **Step 2: Verify frontmatter parses**

Run: `python3 -c "import re; t=open('profil-migrator-lovable/skills/verify-migration/SKILL.md').read(); m=re.match(r'^---\n(.*?)\n---', t, re.S); assert m and 'name:' in m.group(1) and 'description:' in m.group(1); print('FRONTMATTER OK')"`
Expected: `FRONTMATTER OK`

- [ ] **Step 3: Commit**

```bash
git add profil-migrator-lovable/skills/verify-migration/SKILL.md
git commit -m "feat(migrator-lovable): add verify-migration skill"
```

---

### Task 8: Author the orchestrator agent (`agents/migrator-lovable.md`)

**Files:**
- Create: `profil-migrator-lovable/agents/migrator-lovable.md`

**Interfaces:**
- Consumes: `$ARGUMENTS` (source path + `--target` repo path) from the command; the 5 skills (Tasks 3–7); the doctrine (Task 2).
- Produces: an end-to-end orchestration that runs the pipeline and returns a migration report.

- [ ] **Step 1: Write `agents/migrator-lovable.md`**

```markdown
---
name: migrator-lovable
description: >
  Use this agent to migrate a Lovable-style mocked React prototype into the
  "station END" paradigm (feature-based, TanStack Router, mocked apiClient
  façade, END i18n, shadcn/ui) with a functional, unit-tested, gate-verified
  result. Triggers via the /migrator-lovable command or when the user asks to
  migrate/port a Lovable proto to the END paradigm. Expects a source proto path
  and a --target END repo path.
model: inherit
color: purple
---

Tu es le sous-agent **migrator-lovable**. Tu migres un prototype Lovable (React mocké) vers le paradigme cible « station END », en produisant un front fonctionnel, testé (Vitest) et vérifié (typecheck + lint + build verts). Données **mockées** derrière une façade à la signature `apiClient` — pas de vraie API.

## Avant de commencer, lis obligatoirement
1. `${CLAUDE_PLUGIN_ROOT}/standards/paradigm-END.md` — contrat du paradigme END.
2. Les `AGENTS.md`/`CLAUDE.md` du repo cible si présents.

## Entrées
- Source (proto Lovable) et cible (`--target`, repo END) passées via `$ARGUMENTS`.

## Pipeline (ordre strict)
1. **analyze-source** sur la source -> `SOURCE_INVENTORY.md`.
2. **extract-target-paradigm** sur la cible -> `TARGET_PARADIGM.resolved.md` (+ divergences).
3. **build-correspondence-map** -> `CORRESPONDENCE_MAP.md` (+ unmapped).
4. **migrate-screen** : d'abord UN écran walking-skeleton (données + routing + i18n).
5. **verify-migration** : gates verts obligatoires sur cet écran AVANT de continuer.
6. Généralise ensuite écran par écran (migrate-screen -> verify-migration), un à la fois.

## Règles
- Walking-skeleton d'abord : ne généralise jamais avant l'écran 1 vert.
- Cas `Unmapped` : signale et demande décision, n'improvise pas.
- Ne déclare rien « migré/vérifié » sans sorties de gates à l'appui.
- Ne lance JAMAIS l'outil Agent/Task pour invoquer un autre sous-agent (tu es déjà un sous-agent — évite les boucles). Utilise tes propres skills et outils.
- Termine par un rapport : écrans migrés, gates, divergences, unmapped restants.
```

- [ ] **Step 2: Verify frontmatter parses**

Run: `python3 -c "import re; t=open('profil-migrator-lovable/agents/migrator-lovable.md').read(); m=re.match(r'^---\n(.*?)\n---', t, re.S); assert m and 'name: migrator-lovable' in m.group(1); print('AGENT FRONTMATTER OK')"`
Expected: `AGENT FRONTMATTER OK`

- [ ] **Step 3: Commit**

```bash
git add profil-migrator-lovable/agents/migrator-lovable.md
git commit -m "feat(migrator-lovable): add orchestrator agent"
```

---

### Task 9: Author the slash command (`commands/migrator-lovable.md`)

**Files:**
- Create: `profil-migrator-lovable/commands/migrator-lovable.md`

**Interfaces:**
- Consumes: `$ARGUMENTS` from the user.
- Produces: dispatches the `migrator-lovable` agent.

- [ ] **Step 1: Write `commands/migrator-lovable.md`**

```markdown
---
description: Migre un proto Lovable vers le paradigme END (sous-agent dédié)
argument-hint: <chemin-proto-source> --target <chemin-repo-END>
---

Lance le sous-agent **migrator-lovable** (outil Task, agent `migrator-lovable`) pour migrer le prototype Lovable vers le paradigme cible « station END », avec front fonctionnel testé et vérifié :

$ARGUMENTS

Le sous-agent enchaîne son pipeline (analyse source -> paradigme cible -> correspondance -> migration walking-skeleton -> vérification gates -> généralisation) et rapporte un résumé clair : écrans migrés, statut des gates, divergences et cas non mappables.
```

- [ ] **Step 2: Verify frontmatter parses**

Run: `python3 -c "import re; t=open('profil-migrator-lovable/commands/migrator-lovable.md').read(); m=re.match(r'^---\n(.*?)\n---', t, re.S); assert m and 'description:' in m.group(1); print('COMMAND FRONTMATTER OK')"`
Expected: `COMMAND FRONTMATTER OK`

- [ ] **Step 3: Commit**

```bash
git add profil-migrator-lovable/commands/migrator-lovable.md
git commit -m "feat(migrator-lovable): add slash command"
```

---

### Task 10: Structural validation of the whole plugin

**Files:**
- Test: no new files — validates the tree from Tasks 1–9.

**Interfaces:**
- Consumes: the full `profil-migrator-lovable/` tree.
- Produces: confidence that the plugin has the required layout and all frontmatter is valid before packaging.

- [ ] **Step 1: Presence check**

Run:
```bash
cd profil-migrator-lovable
for f in .claude-plugin/plugin.json README.md CHANGELOG.md standards/paradigm-END.md \
         agents/migrator-lovable.md commands/migrator-lovable.md \
         skills/analyze-source/SKILL.md skills/extract-target-paradigm/SKILL.md \
         skills/build-correspondence-map/SKILL.md skills/migrate-screen/SKILL.md \
         skills/verify-migration/SKILL.md; do
  [ -f "$f" ] && echo "OK  $f" || echo "MISSING  $f"
done
cd ..
```
Expected: 11 lines all starting `OK`.

- [ ] **Step 2: Validate every SKILL/agent/command frontmatter in one pass**

Run:
```bash
python3 - <<'PY'
import re, glob
bad=[]
for f in glob.glob('profil-migrator-lovable/**/*.md', recursive=True):
    base=f.split('/')[-1]
    if base in ('README.md','CHANGELOG.md') or '/standards/' in f: continue
    t=open(f).read()
    m=re.match(r'^---\n(.*?)\n---', t, re.S)
    if not (m and 'description:' in m.group(1)):
        bad.append(f)
print('ALL FRONTMATTER OK' if not bad else 'BAD: '+', '.join(bad))
PY
```
Expected: `ALL FRONTMATTER OK`

- [ ] **Step 3: Commit (if any fixes were needed)**

```bash
git add -A profil-migrator-lovable/
git commit -m "chore(migrator-lovable): structural validation fixes" || echo "nothing to fix"
```

---

### Task 11: Golden-case dry run + package the plugin

**Files:**
- Create: `profil-migrator-lovable.plugin` (zip archive at repo root, matching sibling plugins).

**Interfaces:**
- Consumes: the validated `profil-migrator-lovable/` tree; the golden case (`seekube/prototype` → `seekube-v3/apps/web`).
- Produces: a packaged `.plugin` zip + a recorded golden-case validation result.

- [ ] **Step 1: Golden-case validation of the pipeline**

Manually walk the agent pipeline against the golden case to confirm the skills produce their artifacts and one skeleton screen migrates green:
- Run `analyze-source` on `/Users/rudybazantay/Documents/projects/seekube/prototype` → confirm `SOURCE_INVENTORY.md` written with the 6 sections.
- Run `extract-target-paradigm` targeting `/Users/rudybazantay/Documents/projects/seekube/seekube-v3/apps/web` → confirm `TARGET_PARADIGM.resolved.md` + divergences.
- Run `build-correspondence-map` → confirm `CORRESPONDENCE_MAP.md` + unmapped list.
- Run `migrate-screen` on one data+routing+i18n screen, then `verify-migration`.
- Record the gate outputs (typecheck/lint/build/Vitest) in the migration report.

Expected: all four gates PASS on the migrated skeleton screen. (This validates the agent, not just the files.)

- [ ] **Step 2: Package to a `.plugin` zip matching repo convention**

Run:
```bash
cd profil-migrator-lovable && zip -r -X ../profil-migrator-lovable.plugin . -x '.git/*' && cd ..
unzip -l profil-migrator-lovable.plugin | tail -5
```
Expected: archive lists `.claude-plugin/plugin.json`, `agents/`, `commands/`, `skills/`, `standards/`.

- [ ] **Step 3: Commit**

```bash
git add profil-migrator-lovable.plugin
git commit -m "chore(migrator-lovable): package plugin v0.1.0"
```

---

## Notes

- If the golden-case dry run (Task 11 Step 1) surfaces missing conventions (e.g. i18n lib differs from the graph's hints), fix `standards/paradigm-END.md` (Task 2) and the affected skill, then re-run. The paradigm doc is the single source of truth for target conventions.
- Keeping source proto path and target repo path as agent arguments (not hardcoded) is what makes the plugin reusable across future Lovable protos.
