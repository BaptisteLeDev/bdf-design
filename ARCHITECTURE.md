# Architecture - @bdf/design

Ce document décrit la structure interne du design system : les **trois couches de
tokens**, le **mécanisme `[data-bot]`**, les **invariants** non négociables et la
**procédure d'ajout d'un bot**.

## Vue d'ensemble

```
bdf-design/
├─ tokens/tokens.css        # source unique de vérité visuelle (3 couches)
├─ components/*.astro        # composants présentationnels, sans couleur en dur
├─ layouts/Base.astro        # racine de page + primitives globales
├─ playground/               # site Astro de démonstration (preuve de build + doc)
├─ package.json              # exports ./tokens.css, ./components/*, ./layouts/*
├─ README.md                 # quoi/pourquoi/install/usage + API des composants
└─ ARCHITECTURE.md           # ce fichier
```

Le système n'a **aucune étape de build**. Les `.astro` et le CSS sont consommés tels
quels par l'Astro du site qui importe le package. Le flux de dépendances est
**unidirectionnel** :

```
sites consommateurs  ─importent→  components / layouts  ─consomment→  tokens
```

Les composants ne connaissent que les **rôles sémantiques** (couche 2). Ils ne lisent
jamais une primitive de bot (couche 1) ni ne décident d'une couleur de bot : c'est le
contexte `[data-bot]` (couche 3) qui réétiquette les rôles autour d'eux.

## Les trois couches de tokens

Tout vit dans `tokens/tokens.css`, organisé en trois couches strictement ordonnées.

### Couche 1 - Primitives

La matière brute, jamais consommée directement par un composant :

- **Neutres tièdes** : `--c-ink-{300..900}`, `--c-paper-{0,50,100,200}`, `--c-line`.
- **Halos de fond** : `--c-glow-{pink,peach,gold,lilac}` (pastels d'origine, désaturés
  en « air », pas en accents).
- **Rampes par bot** : pour chaque bot,
  `--bot-<id>-{100,200,300,500,600}` + `--bot-<id>-ink`.
  - `100` : wash de fond. `200` : bordures/surfaces accent. `300` : remplissage vif /
    dégradés. `500` : couleur interactive (boutons, marqueurs). `600` : **texte/hover sur
    clair**, garanti AA. `ink` : **texte foncé sur surface accent** (ex. CTA band), AA.
- **États sémantiques** : `--c-positive`, `--c-warning`, `--c-danger`, chacun avec
  `-text` (AA sur son `-100`) et `-100` (surface).
- **Typo / espacement / rayons / ombres / motion / z-index** : échelles partagées.

### Couche 2 - Rôles sémantiques

Les variables **invariantes** que les composants consomment. Au niveau `:root`, elles
pointent vers le bot « lead » (Moodioos) :

```css
--accent-100..600, --accent-ink   /* l'accent actif */
--bot-blob                          /* la forme signature active */
--glow-accent, --ring               /* dérivés de l'accent actif */
--shadow-*, --font-*, --fs-*, --sp-*, --r-*, --ease-*, --dur-*
```

Un composant écrit `background: var(--accent-500)`, jamais `var(--bot-moodioos-500)`.

### Couche 3 - Variantes `[data-bot]`

Quatre sélecteurs `[data-bot="<id>"]` **réétiquettent** la couche 2 dans leur sous-arbre :

```css
[data-bot="renamioos"] {
  --accent-100: var(--bot-renamioos-100);
  /* … 200, 300, 500, 600, ink … */
  --bot-blob: 38% 62% 63% 37% / 41% 44% 56% 59%;  /* pétale étiré */
}
```

Comme `--glow-accent` et `--ring` sont **dérivés** de `--accent-*`, ils suivent
automatiquement le bot actif. C'est là toute la force du mécanisme : on rebinde 7
variables, et l'ensemble du système (boutons, halos, focus, blobs) se recolore.

## Mécanisme `[data-bot]`

```
<html data-bot="moodioos">        ← Base.astro (prop `bot`) : accent de toute la page
  …
  <article data-bot="renamioos">  ← BotCard : surcharge locale, son intérieur vire lilas
    var(--accent-500) ⟶ lilas
  </article>
</html>
```

Règles :

1. **Cascade CSS native** : `data-bot` agit par héritage de custom properties. Pas de
   JS, pas de classe à propager. Le plus proche ancêtre `[data-bot]` gagne.
2. **`Base` pose l'accent global** via la prop `bot` → `data-bot` sur `<html>`.
3. **`BotCard` pose son propre `data-bot`** : on peut afficher les 4 bots côte à côte sur
   une page sans accent global, chacun garde sa teinte.
4. **Les états ignorent `data-bot`** : `StatusPill` utilise les tokens sémantiques
   (`--c-positive/-warning/-danger`), pas `--accent-*`. Un « down » est rouge quel que
   soit le bot.

## Flux de données (provenance visuelle)

Il n'y a **qu'une seule source** de décision esthétique : `tokens/tokens.css`. Aucune
donnée de couleur, d'ombre, de rayon ou de fonte ne vit ailleurs. Changer la charte =
toucher ce fichier, rien d'autre. C'est l'équivalent visuel du « store de provenance » :
les composants sont des consommateurs purs.

## Invariants (non négociables)

1. **Aucune couleur hors tokens.** Pas de hex, `rgb()`, `hsl()` codé en dur dans un
   composant ou un layout (sauf `#fff` / `rgba(255,255,255,…)` purement utilitaires pour
   les reflets/voiles, qui ne sont pas des couleurs de marque). Toute teinte passe par une
   custom property.
2. **Contraste WCAG AA.** Tout texte sur surface utilise un palier prévu pour : `-600`
   sur clair, `-text` sur `-100`, `-ink` sur `-300/-500`. Vérifié (≥ 4.5:1 corps,
   ≥ 3:1 grands titres). Les rampes ne sont pas modifiables sans re-vérifier le contraste.
3. **Motion `transform`/`opacity` uniquement.** Aucune animation de `width`, `top`,
   `background-position` qui déclenche layout/paint. Toute animation est désactivée sous
   `prefers-reduced-motion: reduce`.
4. **Focus visible partout.** `:focus-visible` applique `--ring` (anneau double contrasté)
   sur tout élément focusable. On ne retire jamais l'outline sans le remplacer.
5. **Composants présentationnels.** Pas de fetch, pas d'état métier, pas de logique de
   données dans `components/`. Ce sont des vues paramétrées par props typées.
6. **`data-bot` réservé à l'accent.** On ne détourne pas `[data-bot]` pour autre chose
   qu'un réétiquetage d'accent + forme.

## Conventions

- **Préfixe `bdf-`** sur toutes les classes globales et de composant, pour éviter les
  collisions avec le CSS d'un site consommateur.
- **Styles de composant** : `<style>` scopé Astro par défaut ; `is:global` réservé au
  layout `Base` pour les primitives partagées (`.bdf-wrap`, `.bdf-btn`, …).
- **Props** : typées via `interface Props` + JSDoc. Valeurs littérales pour les unions
  (jamais une couleur libre).

## Ajouter un bot

Procédure complète (exemple fictif `pollioos`, accent « bleu ciel ») :

1. **Tokens - couche 1** : ajouter la rampe dans `tokens/tokens.css`, section *Rampes par
   bot* :
   ```css
   --bot-pollioos-100: …; --bot-pollioos-200: …; --bot-pollioos-300: …;
   --bot-pollioos-500: …; --bot-pollioos-600: …;  /* AA sur paper */
   --bot-pollioos-ink: …;                          /* AA sur -300/-500 */
   ```
   Désaturer le pastel d'origine juste assez pour le premium, rester chaud/cohérent.
2. **Vérifier le contraste** : `-600` ≥ 4.5:1 sur `--c-paper-0`, `-ink` ≥ 4.5:1 sur
   `-300`. Ajuster les valeurs jusqu'à validation (ne pas « tricher » l'invariant 2).
3. **Tokens - couche 3** : ajouter le bloc `[data-bot="pollioos"]` qui rebinde
   `--accent-*` + définit `--bot-blob` (la forme signature propre au bot).
4. **Types** : étendre l'union `"moodioos"|"renamioos"|"coverioos"|"collabioos"` dans
   `Base.astro` et `BotCard.astro` (et tout endroit qui la déclare).
5. **Doc** : ligne dans le tableau des accents du `README.md`.
6. **Playground** : ajouter une entrée au tableau `bots` de
   `playground/src/pages/index.astro` - la démo le couvrira automatiquement.
7. **Build** : `cd playground && bun run build` doit passer.

Aucun composant n'a besoin d'être modifié : ils consomment déjà `--accent-*` / `--bot-blob`.
