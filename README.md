# @bdf/design

Design system de la flotte **BotDiscordFactory** (la famille `-ioos`). Un seul langage
visuel (typographie, espacement, hairlines, formes nettes) décliné par **un accent
de couleur par bot** via l'attribut `[data-bot]`.

Direction artistique : **« Neo-grotesque minimal »**. Base neutre quasi-monochrome
(encre presque noire sur papier blanc cassé), typographie neo-grotesque à fort contraste
de graisse (`Space Grotesk` display + `Geist` corps/UI, mono système), ornement réduit
(plus de blobs organiques : rayons serrés, formes géométriques nettes), ombres discrètes,
accent unique par bot conservé mais employé avec parcimonie.

## Quoi / pourquoi

- **Quoi** : un package Astro qui exporte des **tokens CSS** (custom properties) et des
  **composants `.astro`** prêts à consommer, plus un **layout de base**.
- **Pourquoi** : les 4 sites de la flotte (`moodioos`, `renamioos`, `coverioos`,
  `collabioos`) partagent ce socle. Changer une couleur, une ombre ou une fonte = un seul
  fichier (`tokens/tokens.css`). Chaque site ne fait que **consommer** le système.
- **Pas de build** : composants `.astro` + CSS consommés tels quels. L'app qui l'importe
  (chaque site, ou le `playground/`) compile via son propre Astro.

## Installation

`@bdf/design` n'est pas publié sur npm. On l'installe en **dépendance locale ou Git**.

### En local (monorepo / chemin relatif)

```jsonc
// package.json du site consommateur
{
  "dependencies": {
    "@bdf/design": "file:../bdf-design",
    "astro": "^5.7.0"
  }
}
```

### Depuis GitHub (quand le remote existera)

```jsonc
{
  "dependencies": {
    "@bdf/design": "github:BaptisteLeDev/bdf-design#main"
  }
}
```

Puis `bun install` (ou `pnpm install` / `npm install`).

> **Astro** est une `peerDependency` (`>=4 <6`) : c'est le site consommateur qui l'apporte.

## Usage

```astro
---
import Base from "@bdf/design/layouts/Base.astro";
import Nav from "@bdf/design/components/Nav.astro";
import Hero from "@bdf/design/components/Hero.astro";
import BotCard from "@bdf/design/components/BotCard.astro";
---
<Base title="Moodioos" description="Le bot bien-être" bot="moodioos">
  <Nav brand="Moodioos" glyph="🦆" cta={{ label: "Ajouter à Discord", href: "#" }} />
  <main>
    <Hero overline="Moodioos · bien-être">
      <Fragment slot="title">Un canard qui veille sur l'humeur du serveur.</Fragment>
      <Fragment slot="lead">Des rituels doux, jamais intrusifs.</Fragment>
      <Fragment slot="actions">
        <a class="bdf-btn" href="#">Ajouter à Discord</a>
      </Fragment>
    </Hero>
  </main>
</Base>
```

`bot="moodioos"` sur `<Base>` pose `data-bot="moodioos"` sur `<html>` : **toute la page**
prend l'accent corail/pêche et la forme goutte. On peut surcharger localement n'importe où
en reposant `data-bot` sur un conteneur (c'est ce que fait `<BotCard>`).

### Tokens seuls (sans les composants)

```css
@import "@bdf/design/tokens.css";
.mon-bouton { background: var(--accent-500); color: #fff; border-radius: var(--r-pill); }
```

## Accents par bot

| Bot          | Accent              | Forme signature (`--bot-blob`) |
| ------------ | ------------------- | ------------------------------ |
| `moodioos`   | corail / pêche      | goutte ronde et joviale        |
| `renamioos`  | lavande / lilas     | pétale étiré                   |
| `coverioos`  | doré / ambre        | galet asymétrique (mystère)    |
| `collabioos` | vert sauge          | feuille                        |

Chaque accent expose une rampe `100 / 200 / 300 / 500 / 600 + -ink` (cf. `ARCHITECTURE.md`).
Le palier `-600` garantit le contraste AA (≥ 4.5:1) pour du texte posé sur `paper-0`, `paper-50`
**et** `paper-100`. Le palier `-ink` est l'accent foncé pour du texte sur surface accent (`-100`).

## Composants (API publique)

Tous les composants consomment l'accent du contexte `[data-bot]` ancêtre. Aucune couleur
n'est passée en prop : on choisit le bot, pas la couleur.

### `layouts/Base.astro`

Racine de page : `<html lang data-bot>`, fontes Google, reset, import des tokens,
primitives globales (`.bdf-wrap`, `.bdf-btn`, `.bdf-overline`, `.bdf-section`, halo
ambient, anneau de focus, `.bdf-reveal`).

| Prop          | Type                                                    | Défaut       | Rôle                                   |
| ------------- | ------------------------------------------------------- | ------------ | -------------------------------------- |
| `title`       | `string` **(requis)**                                   | -            | `<title>` du document                  |
| `description` | `string`                                                | -            | meta description                       |
| `bot`         | `"moodioos"\|"renamioos"\|"coverioos"\|"collabioos"`    | -            | accent global de la page               |
| `lang`        | `string`                                                | `"fr"`       | langue du document                     |
| `ambient`     | `boolean`                                               | `true`       | halo de lumière de fond                |
| `bodyClass`   | `string`                                                | `""`         | classes additionnelles sur `<body>`    |

Slots : `head` (injection `<head>`), default (contenu de page).

### `components/Nav.astro`

Barre sticky, ombre/bordure au scroll (script vanilla inline).

| Prop          | Type                                              | Défaut                     |
| ------------- | ------------------------------------------------- | -------------------------- |
| `brand`       | `string` **(requis)**                             | -                          |
| `brandSuffix` | `string`                                          | -                          |
| `href`        | `string`                                          | `"/"`                      |
| `glyph`       | `string`                                          | - (sinon marque flotte)    |
| `links`       | `{ label, href, current? }[]`                     | `[]`                       |
| `cta`         | `{ label, href }`                                 | -                          |
| `ctaVariant`  | `"solid" \| "ghost"`                              | `"solid"`                  |
| `ariaLabel`   | `string`                                          | `"Navigation principale"`  |

### `components/Footer.astro`

| Prop          | Type                                                | Défaut |
| ------------- | --------------------------------------------------- | ------ |
| `brand`       | `string` **(requis)**                               | -      |
| `brandSuffix` | `string`                                            | -      |
| `href`        | `string`                                            | `"/"`  |
| `glyph`       | `string`                                            | -      |
| `tagline`     | `string`                                            | -      |
| `columns`     | `{ title, links: { label, href }[] }[]`             | `[]`   |
| `legal`       | `string`                                            | -      |
| `version`     | `string`                                            | -      |

### `components/Hero.astro`

En-tête deux colonnes. Sans slot `visual`, passe en pleine largeur centrée.

| Prop         | Type                       | Défaut      |
| ------------ | -------------------------- | ----------- |
| `overline`   | `string`                   | -           |
| `titleStyle` | `"default" \| "gradient"`  | `"default"` |
| `align`      | `"start" \| "center"`      | `"start"`   |

Slots : `title` **(requis)**, `lead`, `actions`, `visual`. En `titleStyle="gradient"`, un
`<em>` dans le slot `title` reçoit le dégradé multi-bots de la flotte.

### `components/BotCard.astro`

Carte de bot. **Pose elle-même `data-bot={bot}`** : l'intérieur hérite de la teinte.

| Prop    | Type                                                  | Défaut       |
| ------- | ----------------------------------------------------- | ------------ |
| `bot`   | `"moodioos"\|"renamioos"\|"coverioos"\|"collabioos"` **(requis)** | - |
| `name`  | `string` **(requis)**                                 | -            |
| `tag`   | `string`                                              | -            |
| `glyph` | `string` **(requis)**                                 | -            |
| `href`  | `string`                                              | `"#"`        |
| `cta`   | `string`                                              | `"Explorer"` |
| `soon`  | `boolean`                                             | `false`      |

Slot : default (description).

### `components/StatCard.astro`

| Prop         | Type                  | Défaut | Rôle                                              |
| ------------ | --------------------- | ------ | ------------------------------------------------- |
| `value`      | `string` **(requis)** | -      | chiffre principal (placeholder si valeur live)    |
| `unit`       | `string`              | -      | suffixe accentué (ex. `k`, `M`, `%`)              |
| `label`      | `string`              | -      | libellé sous le chiffre                           |
| `overline`   | `string`              | -      | sur-titre mono                                    |
| `valueId`    | `string`              | -      | `id` sur le chiffre, pour injection live (client) |
| `overlineKey`| `string`              | -      | `data-i18n` sur le sur-titre (hook i18n client)   |

### `components/CommandCard.astro`

| Prop    | Type                  | Défaut |
| ------- | --------------------- | ------ |
| `name`  | `string` **(requis)** | -      |
| `glyph` | `string` **(requis)** | -      |

Slot : default (description). À placer dans un `<CommandGrid>`.

### `components/CommandGrid.astro`

| Prop      | Type            | Défaut |
| --------- | --------------- | ------ |
| `columns` | `2 \| 3 \| 4`   | `3`    |

Slot : default (`<CommandCard>`).

### `components/CtaBand.astro`

Bandeau accent. Texte en `--accent-ink` (AA garanti).

| Prop       | Type                  | Défaut |
| ---------- | --------------------- | ------ |
| `overline` | `string`              | -      |
| `title`    | `string` **(requis)** | -      |
| `decor`    | `boolean`             | `true` |

Slots : `lead`, `actions`.

### `components/StatusPill.astro`

Couleur **sémantique** (verte / ambre / rouge), jamais l'accent du bot.

| Prop     | Type                          | Défaut             |
| -------- | ----------------------------- | ------------------ |
| `status` | `"up"\|"degraded"\|"down"` **(requis)** | -        |
| `label`  | `string`                      | libellé par défaut |
| `pulse`  | `boolean`                     | `true`             |

### `components/BlobShape.astro`

Écrin organique pour un emoji/glyphe.

| Prop    | Type                                  | Défaut    |
| ------- | ------------------------------------- | --------- |
| `shape` | `"blob"\|"pill"\|"round"\|"soft"`     | `"blob"`  |
| `size`  | `string` (CSS)                        | `"60px"`  |
| `fill`  | `"tint"\|"solid"\|"plain"`            | `"solid"` |
| `float` | `boolean`                             | `false`   |
| `class` | `string`                              | `""`      |

Slot : default (emoji / glyphe / SVG, `aria-hidden`).

## Playground (preuve + doc visuelle)

`playground/` est un mini-site Astro qui consomme `@bdf/design` en `file:..` et rend
**chaque composant dans les 4 accents**. Il sert de preuve de build et de documentation
visuelle.

```bash
cd playground
bun install
bun run build      # -> playground/dist/  (build statique : la preuve)
bun run dev        # http://localhost:4321  (prévisualisation locale)
bun run preview    # sert le build de dist/
```

> **Choix d'outil** : `bun 1.3.11` fonctionne pour `install`, `build`, `dev` et `preview`
> du playground sur Windows natif (Astro 5.x). Aucun fallback `pnpm`/`npm` n'a été
> nécessaire. En cas de souci ultérieur, ces deux gestionnaires restent compatibles
> (mêmes scripts).

## Voir aussi

- [`ARCHITECTURE.md`](./ARCHITECTURE.md) - les 3 couches de tokens, le mécanisme
  `[data-bot]`, les invariants et la procédure pour **ajouter un bot**.
