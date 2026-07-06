# profil-migrator-lovable — Correction des manques (v0.6.0)

> Plan de correction des lacunes du pipeline identifiées après réécriture du paradigme (ancré `apps/app`). Périmètre : lot 🔴 (bloquant) + 🟠 (important). Les 🟡 sont conservés en backlog (hors périmètre v0.6.0).

## Contexte
Pipeline actuel (INIT via `/profil-migrator-lovable:init` + agent `migrator-lovable`, 11 étapes). Manques détectés : socle d'app absent pour app dédiée, pas d'`install`/baseline, `verify-migration` codé en dur `apps/web`, artefacts dispersés, design non aligné sur `@seekube/brand`.

## Périmètre v0.6.0

### 1. 🔴 Nouveau skill `scaffold-app-shell`
- Fichier : `skills/scaffold-app-shell/SKILL.md`.
- Pose le socle AVANT l'écran 1 (app dédiée = `src/` vidé par l'init) :
  - `src/router.tsx`
  - `src/routes/__root.tsx` : QueryClientProvider (`staleTime 60_000`, `retry false`), Header/Footer, `Toaster` (sonner), `ErrorFallback`/`NotFound`, `head()` meta via i18n.
  - `src/lib/i18n.ts` (ns `common` fr/en, `defaultNS`, `SUPPORTED_LANGUAGES`, `setLanguage`).
  - `src/lib/utils.ts` (`cn`, `toErrorMessage`).
  - `src/lib/api/client.ts` (mutator `apiClient`).
  - `src/styles.css` (`@import 'tailwindcss'` + `@plugin typography` + `tw-animate-css` + `@import '@seekube/brand/tokens.css'` + `@custom-variant dark` + `@layer base`).
  - `src/templates/common/{ErrorFallback,NotFound}`.
  - primitives `src/components/ui/` de base.
- Gabarit = `apps/web` / `apps/app` (copie fidèle, vidée du métier).
- **Idempotent** : app existante → vérifie seulement, ne recrée rien.
- Sortie → `SHELL_MAP.md`.
- Pipeline : étape **7bis** (après design, avant `migrate-screen`).

### 2. 🔴🟠 `pnpm install` + baseline verte
- Dans `initialize-migration` (étape finale) : `pnpm install` depuis racine station.
- Baseline : `tsc --noEmit` + `build` sur la destination → doit être vert AVANT migration (sinon impossible d'isoler un échec de migration du socle).
- Rapport init = statut baseline.

### 3. 🔴 Fix `verify-migration` codé en dur
- Remplacer `apps/web` par la destination réelle `apps/<nom>` (entrée du skill). Gates lancées depuis ce dossier.

### 4. 🟠 Standardiser les artefacts
- Tous les `*.md` du pipeline (`SOURCE_INVENTORY`, `TARGET_PARADIGM.resolved`, `CORRESPONDENCE_MAP`, `ASSETS_MAP`, `SHADCN_VARIABLES`, `BRIDGE_MAP`, `DESIGN_MAP`, `SHELL_MAP`) → dossier unique **`<target>/.plans/migration/`**.
- Corriger chemins entrée/sortie dans les 8 skills concernés + agent.

### 5. 🟠 Aligner design sur brand tokens
- `apply-shadcn-variables` : mapper proto → tokens `@seekube/brand` / classes sémantiques (`bg-background`, `text-foreground`, `border-border`…). Bridge = reliquat non mappable seulement.
- Règle : ne jamais écrire la palette dans `styles.css`.

### Intendance
- `agents/migrator-lovable.md` : étape 7bis + prérequis baseline + chemins `.plans/migration/`.
- `README.md` : ajout `scaffold-app-shell` + renumérotation.
- `plugin.json` → `0.6.0`, `CHANGELOG.md`.
- Repackage `profil-migrator-lovable.plugin`.

## Ordre d'exécution
3 → 4 → 1 → 2 → 5 → intendance → repackage.

## Fichiers touchés
~13 : 1 neuf (`scaffold-app-shell`), 8 skills (chemins), `verify-migration`, `initialize-migration`, `apply-shadcn-variables`, agent, README, plugin.json, CHANGELOG.

---

## Backlog 🟡 (hors v0.6.0, conservé)
7. **Smoke runtime** : gates ne lancent pas l'app ; front mocké peut crasher (providers/hydratation) → smoke `pnpm dev` + preview.
8. **Forms non imposés** : §0 = react-hook-form + zod ; aucun skill ne l'exige explicitement.
9. **Réconciliation dépendances** : lib du proto absente d'END → décision ajouter/remplacer non cadrée.
10. **Étape commit/handoff** : pipeline s'arrête à l'audit (probablement volontaire).

## Suivi
- [x] 1. scaffold-app-shell
- [x] 2. install + baseline
- [x] 3. fix verify-migration
- [x] 4. artefacts .plans/migration/
- [x] 5. design brand tokens
- [x] intendance + repackage

✅ Réalisé le 2026-07-06 — plugin repackagé en v0.6.0.
