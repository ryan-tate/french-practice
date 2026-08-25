# French Practice

Self-scoring French grammar drills, served at **[french-practice.com](https://french-practice.com)**.

Every drill is a single self-contained HTML file — no build step, no dependencies, no backend,
no JavaScript bundle. Open one locally and it works exactly as it does on the site.

## Layout

```
/
├── index.html          home page — links to each drill
├── README.md
├── passe-compose/
│   └── index.html      Passé composé · participes passés
├── nombres/
│   └── index.html      Les nombres
└── bilan/
    └── index.html      renders a shared summary from the URL fragment
```

Each drill lives in its own folder as `index.html`, so its URL has no file extension:
`french-practice.com/passe-compose/`.

## Adding a drill

1. Create a folder named for the drill (`imparfait/`, `subjonctif/`, …).
2. Put the drill in it as `index.html`.
3. In the root `index.html`, copy the `<a class="drill">` block, point its `href` at the new
   folder, and update the title, tags and description. There's a comment marking the spot.

Styles are duplicated in each file rather than shared through a stylesheet. That's deliberate:
it keeps every drill downloadable and usable offline as a single file. The token block at the
top of each `<style>` is the same in all of them — copy it into a new drill to inherit the
palette and both themes.

## Drills

### Passé composé — participes passés

70 verbs, in three exercises:

| Mode | Prompt | You answer |
|---|---|---|
| **Participe** | `vivre → ______` | the past participle |
| **Auxiliaire** | `se promener → ______ promené` | avoir, être, or s'être |
| **Forme complète** | `elles ______ (partir)` | the whole conjugated phrase, agreement included |
| **Mixte** | — | rotates through all three |

**Scope:** all 70, colonne 1, colonne 2, or only verbs you've missed before. Draws are even
across the list; anything you get wrong is weighted up and resurfaces until you clear it.

**Negation:** Affirmatif / Négatif / Mélangé, rotating *ne… pas*, *ne… jamais*, *ne… plus*.
Negatives are offered only in Forme complète, since they need the whole verb phrase.

**Marking:**

- Accents count. `vecu` for `vécu` is marked **presque**, not accepted.
- Where the subject's gender is ambiguous, both agreements pass — `je suis allé` and
  `je suis allée` are equally correct; `vous` accepts all four forms.
- Apostrophes, curly quotes, trailing punctuation and stray whitespace are forgiven.
- **avoir** is blue and **être** is ochre throughout, so the split stays visible.

Two entries on the source sheet are typos, corrected here: `demander → demandé` and
`rentrer → rentré`. Note also that **monter, descendre, sortir, rentrer** and **retourner** are
listed as *être* verbs but take *avoir* when they have a direct object — *il **a** monté les
escaliers*. The drill says so when those come up.

### Les nombres

Numbers are generated rather than drawn from a fixed list, so the drill never repeats itself.
Both directions: **chiffres → lettres** (see `96`, write *quatre-vingt-seize*) and
**lettres → chiffres**.

**Scopes:** `0–100`, `70–99` on its own, `centaines & milliers`, `années`, `prix`,
`téléphone`.

What's tracked is the **rule**, not the number — miss `96` and the *80–99* rule gets weighted
up, so the next few questions probe the same soft spot with different figures. The ten rules
are 0–20, « et un », 20–69, 70–79, 80–99, centaines, milliers, années, prix, téléphone.

**Marking:**

- Hyphens and spaces are treated as equivalent, so both the traditional spelling
  (*quatre-vingt-dix-sept*, *deux cent un*) and the fully hyphenated post-1990 reform spelling
  are accepted.
- Years from 1100–1999 accept either reading: *mille neuf cent quatre-vingt-quatre* or
  *dix-neuf cent quatre-vingt-quatre*.
- Prices accept *vingt-quatre euros cinquante* and *…cinquante centimes*.
- Phone numbers are drilled words→digits only, since the real skill is hearing a number read
  in pairs and writing it down.
- The `-s` rules are enforced exactly: *quatre-vingts* but *quatre-vingt-un* and
  *quatre-vingt mille*; *deux cents* but *deux cent un*; *mille* never pluralised.

Belgian and Swiss *septante / octante / huitante / nonante* are noted in the page footer but
not drilled.

## Sharing a bilan

Each drill has two export buttons under its review table:

- **Copier le résumé** — a plain-text summary for pasting into an email or a message.
- **Copier le lien du bilan** — a URL to `/bilan/#<payload>` that renders the results as a page.

Both read `localStorage` for *all* drills, so one export covers everything that browser has done.

The payload is deliberately tiny — roughly 100 characters for both drills — and sits in the URL
**fragment**, which browsers never send to the server. The figures travel only inside the message
the student sends; GitHub never sees them, and `/bilan/` stores nothing when it renders them.

Format: `1~pc<best>.<answered>.<correct>.<70 chars>~nb<best>.<answered>.<correct>.<10 chars>`.
Counts are base-36; each miss count is one base-32 character holding `round(miss * 2)`, capped at
31, in the fixed key order defined by `PC_ORDER` / `NB_ORDER`. Adding a verb or a rule means
bumping the leading version number so old links still fail cleanly rather than decoding wrong.

Nothing is authenticated — anyone can hand-edit a payload. It's a "here's what I'm struggling
with" artifact, not a grade.

## Privacy

Progress is kept in the browser's `localStorage`. It leaves the device only when the user clicks
one of the export buttons, and then only into whatever message they paste it in. No server, no
analytics, no cookies, no accounts. The only outbound request any page makes is to Google Fonts
for the stylesheet; offline, the pages fall back to system fonts and still work. Each visitor's
history is their own, and nobody — including whoever hosts the site — can see anyone else's.

## Deployment

GitHub Pages from the `main` branch, root folder, with a custom domain.

DNS for the apex, four `A` records:

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

and optionally the matching `AAAA` records (`2606:50c0:800{0,1,2,3}::153`). `www` is a `CNAME`
to `<username>.github.io`. The `CNAME` file in the repo root is written by GitHub when the
custom domain is set — don't delete it. Enable **Enforce HTTPS** once the certificate finishes
provisioning, which can take up to 24 hours after DNS resolves.

## Credits

Verb list for the passé composé drill from *Le passé composé · participes passés* by
Marine Vuigner, FLE Nantes.