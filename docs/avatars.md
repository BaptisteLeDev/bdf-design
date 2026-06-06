# Avatars de la flotte, prompts Nano Banana

> Prompts pour générer les photos de profil Discord des bots (Nano Banana / Gemini image).
> Référence visuelle : l'avatar Moodioos existant (canard kawaii flat sur fond jaune pastel).
> Les fonds reprennent les accents `[data-bot]` de tokens.css pour que l'avatar Discord,
> la landing et le portail racontent la même identité.

## Direction artistique commune (à coller en tête de CHAQUE prompt)

```text
Minimal flat kawaii mascot avatar for a Discord bot. Square 1:1. The mascot's face
fills about 60% of the frame, perfectly centered, cropped like a close-up portrait.
Two big round glossy black eyes, tiny soft pink blush circles on the cheeks.
Solid pastel background, soft flat vector shading, NO outlines, NO text, NO watermark,
NO hands, NO full body. One single signature accessory maximum. Soft studio light,
subtle grain. Cute, friendly, premium, children's book illustration style.
```

Anti-dérive (à ajouter si le modèle brode) : `no 3D render, no gradient background,
no realistic fur or feathers, no extra characters, no border, no frame`.

## Spécifications de sortie

- Format carré, générer en 1024×1024 minimum (Discord descend tout seul à 128).
- PNG. Vérifier la lisibilité en 40×40 (taille réelle en liste de membres) : si
  l'accessoire disparaît, le grossir et regénérer.
- Un avatar = UNE idée. Si tu hésites entre deux accessoires, deux générations.

## Moodioos (référence, déjà en prod)

Fond jaune pastel, canard, nœud papillon rouge. Prompt de regénération si besoin
d'une version plus grande ou d'une déclinaison :

```text
[direction artistique commune]
The mascot is a cute duck: round yellow head on a soft pastel yellow background
(#FCEFA6), small orange cartoon beak with two tiny nostrils, big round black eyes,
pink blush cheeks, and a neat red bow tie at the bottom center.
The duck's head color is slightly more saturated than the background.
```

## ReNamioos (proposition : caméléon, à valider)

Le bot qui change ton pseudo de style = l'animal qui change d'apparence. Accent
lavande `#f1e7f7` / `#9b6fc9`.

```text
[direction artistique commune]
The mascot is a cute chameleon: round pastel purple head on a soft lavender
background (#F1E7F7), big round black eyes looking slightly in two different
directions (subtle, still cute), pink blush cheeks, a tiny curled tail visible
at the bottom corner, and a small color-swatch shaped crest on top of the head
in deeper purple (#9B6FC9).
```

## Coverioos (LE masque est la mascotte)

Le jeu Undercover = identités cachées. Pas un personnage qui porte un masque :
l'avatar EST le masque, seul, flottant au centre. Les trous des yeux jouent le rôle
des grands yeux noirs ronds de la flotte. Accent doré `#fcf0d4` / `#cf9a23`.

```text
[direction artistique commune]
The mascot IS a masquerade mask itself, floating alone in the center of the frame,
no face and no person behind it: a cute warm gold venetian domino mask (#CF9A23)
on a soft cream background (#FCF0D4). The two eye holes of the mask are big round
glossy black shapes, exactly like cartoon eyes. Tiny pink blush circles painted
on the lower corners of the mask. A thin darker gold ribbon curls away from each
side of the mask. Slightly mysterious but adorable.
```

Variantes si le rendu déçoit : `theater mask` (masque de théâtre arrondi) ou
`phantom mask` ; garder les trous d'yeux = yeux noirs ronds, c'est la signature.

## Collabioos (une pp qui dit « collab »)

Pas d'animal : le sujet, c'est la COLLABORATION. Deux petites bouilles rondes joue
contre joue, yeux en cœurs (le coup de cœur entre streamers), un seul duo qui
remplit le cadre comme une seule mascotte. Accent sauge `#e8f1e6` / `#6f9c5f`.

```text
[direction artistique commune, en remplaçant "The mascot's face" par "The duo"]
The mascot is a DUO: two cute round blob faces side by side, cheek pressed against
cheek, together filling the center of the frame like one single mascot. Left blob
is soft sage green (#6F9C5F desaturated), right blob is soft cream. Both have big
glossy RED HEART-SHAPED eyes (in love) and pink blush cheeks. One tiny red heart
floats just above the point where their cheeks touch. Same size, both smiling,
clearly a team.
```

Variantes : deux bulles de dialogue arrondies qui se chevauchent en formant un
cœur à l'intersection (plus abstrait), ou le duo qui se tape dans la main
(« high five », attention : les mains rendent mal en petit). Garder les yeux en
cœurs quoi qu'il arrive.

## Checklist après génération

1. Les 4 avatars côte à côte : même langage (fond pastel uni, yeux noirs ronds
   sauf Collabioos, blush, un accessoire). Si un avatar « sort du lot », regénérer.
2. Tester en 40×40 sur fond sombre ET clair (thèmes Discord).
3. Ranger les sources dans `sites/bdf-design/assets/avatars/` (à créer) :
   `moodioos.png`, `renamioos.png`, `coverioos.png`, `collabioos.png`.
