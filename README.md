# Passé Composé Drill

A single-file, self-scoring drill for the 70 past participles on the *Le passé composé ·
participes passés* reference sheet. No build step, no dependencies, no backend — one
`index.html` you can open locally or host anywhere static.

## Exercises

| Mode | Prompt | You answer |
|---|---|---|
| **Participe** | `vivre → ______` | the past participle |
| **Auxiliaire** | `se promener → ______ promené` | avoir, être, or s'être |
| **Forme complète** | `elles ______ (partir)` | the whole conjugated phrase, agreement included |
| **Mixte** | — | rotates through all three |

**Verb scope:** all 70, colonne 1 only, colonne 2 only, or just the verbs you've missed
before. Draws are even across the list; anything you get wrong is weighted up and keeps
resurfacing until you clear it.

**Negation:** Affirmatif / Négatif / Mélangé, rotating *ne… pas*, *ne… jamais* and
*ne… plus*. Negatives are only offered in Forme complète, since they need the whole verb
phrase to be worth practising.

## Marking

- Accents are graded. `vecu` for `vécu` is marked **presque**, not accepted — in French an
  accent is a spelling mistake, not a decoration.
- Where the subject's gender is ambiguous, both agreements pass: `je suis allé` and
  `je suis allée` are equally correct. `vous` accepts all four forms.
- Apostrophes, curly quotes, trailing punctuation and extra whitespace are all forgiven.
- Auxiliary colour-coding runs throughout the interface: **avoir** in blue, **être** in
  ochre, so the split you have to memorise is always visible.

## Privacy

Progress is stored in the browser's `localStorage` and never leaves the device. There is no
server, no analytics, no cookies, and no network request other than the Google Fonts
stylesheet. Every visitor's history is their own; nobody — including whoever hosts it — can
see anyone else's. "Effacer mes statistiques" wipes it.

## Running it

**Locally:** download `index.html` and open it. That's the whole thing.

**GitHub Pages:** put `index.html` in the repo root, then Settings → Pages → Source:
*Deploy from a branch*, branch `main`, folder `/ (root)`. The site appears at
`https://<username>.github.io/<repo>/` within a minute or so.

**Anywhere else static** — Netlify, Cloudflare Pages, S3, a USB stick — works the same way.
Keep the filename `index.html` so the bare folder URL serves the drill.

Offline the page still works; it falls back to system fonts when Google Fonts is
unreachable.

## Corrections to the source sheet

Two entries on the original PDF are typos, fixed here:

- `demander → demander` should be **demandé**
- `rentrer → rentrer` should be **rentré**

Also worth knowing, and noted in the app's feedback: **monter, descendre, sortir, rentrer**
and **retourner** are listed as *être* verbs, but take *avoir* whenever they have a direct
object — *il **a** monté les escaliers*, *j'**ai** sorti les poubelles*.

## Credits

Verb list from *Le passé composé · participes passés* by Marine Vuigner, FLE Nantes.
The drill itself is just a wrapper around her reference sheet.
