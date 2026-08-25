# CLAUDE.md — french-practice.com

Self-scoring French grammar drills. Static site on GitHub Pages, custom domain via Route 53.

## Hard constraints — do not break these without being asked

1. **No build step.** Every page is one hand-written `.html` file with inline `<style>` and
   `<script>`. No bundler, no npm dependency, no framework, no transpile. If a change seems to
   need tooling, the change is wrong.
2. **Every drill is self-contained.** A drill must work when saved to a desktop and opened from
   `file://`, offline. This is why CSS is duplicated across files instead of shared — that
   duplication is deliberate, not tech debt. Do not "fix" it by extracting a stylesheet.
3. **No backend, ever.** No fetch to any origin, no analytics, no cookies, no accounts. The only
   permitted outbound request is the Google Fonts stylesheet, and pages must degrade to system
   fonts without it. A backend was evaluated and deliberately rejected (see *Rejected* below).
4. **Progress stays in `localStorage`.** It leaves the device only when a user clicks an export
   button. The privacy copy on the home page must stay accurate to whatever the code actually does.
5. **Never mark a correct answer wrong.** A drill that rejects valid French is worse than no
   drill. Accept every legitimate variant; when unsure whether a form is valid, accept it.

## Layout

```
/
├── index.html          home page, links to each drill
├── CLAUDE.md
├── README.md
├── passe-compose/index.html
├── nombres/index.html
├── interrogatives/index.html
└── bilan/index.html    renders a shared summary from the URL fragment
```

Each drill is a folder with `index.html` inside, so URLs have no file extension. Keep it that way.
GitHub writes a `CNAME` file at the root when the custom domain is set — don't delete it.

## Design system

Copy the token block from an existing drill. Do not invent new colors.

- **Palette:** paper `#EFEEE9`, card `#FBFAF7`, ink `#171B24`, rule `#D8D6CE`; accents
  avoir-blue `#3B4A8C` and être-ochre `#A5661C`; juste `#2E6B4A`, faux `#A93B2C`,
  presque `#8A6A12`. Dark variants are in the same block.
- **Type:** Bodoni Moda (display + big numerals), Libre Franklin (body), IBM Plex Mono (labels,
  answers, data). Always with real fallback stacks.
- **Themes, three states.** Bare `:root` holds the full light palette;
  `@media (prefers-color-scheme: dark)` guarded as `:root:not([data-theme="light"])` redefines
  *only tokens*; `:root[data-theme="dark"]` redefines them again. Never declare a color solely
  inside a media or `[data-theme]` block — that is the classic unreadable-page bug.
- The accent pair carries meaning: **avoir is blue, être is ochre**, everywhere. Reuse that pair
  only for genuine binaries, not decoration.

## Anatomy of a drill

All drills share the same skeleton, in this order: masthead → control rows (chips) → four-stat
scoreboard → question card → review table → export row → footer.

State shape in `localStorage`:

- `pc-drill-v3` → `{stats: {<verb>: {seen, miss}}, best, n, c}`
- `nb-drill-v1` → `{miss: {<rule>: n}, best, n, c}`
- `in-drill-v1` → `{miss: {<rule>: n}, best, n, c}`

`n` = questions answered, `c` = correct. Missed items are weighted up on selection
(`1 + 1.7 × miss`) and decay by 0.5 on a correct answer. Accent-only errors score `presque`,
count as 0.5 miss, and break the streak.

## Adding a drill

1. Copy the closest existing drill as a starting skeleton.
2. Keep the same control-row / scoreboard / card / review structure.
3. Bump the `localStorage` key if the shape changes; never silently reinterpret an old key.
4. Add an `<a class="drill">` card to the root `index.html` — there's a comment marking the spot.
5. Add a section to `README.md`.
6. If the drill should be exportable, add its key to the bilan encoder (see below).

## Bilan export contract

Two buttons under every review table: plain-text summary, and a link to
`/bilan/#<payload>`. Both read `localStorage` for *all* drills, so one export covers everything.

```
1~pc<best>.<answered>.<correct>.<70 chars>~nb<best>.<answered>.<correct>.<10 chars>~in<best>.<answered>.<correct>.<6 chars>
```

Counts are base-36. Each miss is one base-32 char holding `round(miss * 2)`, capped at 31, in the
fixed order of `PC_ORDER` / `NB_ORDER` / `IN_ORDER`. `<correct>` is stored as **correct + 1** so `0` means
"not recorded" — the report then omits the score line rather than showing a fake 0%.

**The payload must stay in the fragment.** Browsers never transmit it, so results never reach
GitHub's logs. Do not move it to a query string.

Segments are keyed by tag, and the decoder looks the tag up in a map — an unknown tag throws
rather than silently decoding against the wrong key list. Adding a whole new drill therefore does
*not* need a version bump; old links keep working.

**Changing `PC_ORDER`, `NB_ORDER` or `IN_ORDER` — including adding one verb — invalidates every
existing link.** Bump the leading version number when you do, so old links fail cleanly instead of
decoding into the wrong rows.

Nothing is authenticated. This is a "here's what I'm struggling with" artifact, not a grade.

## Verification — do this, it has caught real bugs

Playwright with the preinstalled Chromium. Never ship a grading change on inspection alone.

```bash
node -e "const {chromium}=require('playwright'); ..."   # executablePath: chromium from PATH
python3 -m http.server 8899                            # clipboard needs http/https, not file://
```

Minimum bar for any change to answer-checking:

1. **Self-answer test.** Drive the page to generate N questions, answer each with its *own*
   accepted form, and assert every one scores `juste`. Zero false negatives. This caught nothing
   the first time only because it was run properly — do run it.
2. **Exhaustive invariant scan** where the answer space is generated rather than listed. The
   numbers drill scans 0–999,999 asserting the `-s` rules hold.
3. **Click the actual buttons.** Calling a function via `evaluate()` is not a test of the button.
   A shipped bug was exactly this: handlers were never bound and `typeof onclick === "object"`.
4. **Assert file edits landed.** If you inject code by string replacement, count the injected
   symbols afterwards. A silent no-match shipped a page with dead buttons.
5. Load every page and assert zero `pageerror`, in both color schemes.

## Gotchas learned the hard way

- **Clipboard needs a secure context.** `navigator.clipboard` is unavailable on `file://`. There
  is a manual-textarea fallback; keep it, and keep the failure message specific.
- **Adding a persisted field breaks returning users.** When `n`/`c` were added, existing users
  had neither and the export silently refused. Always derive or default when reading old state.
- **Accents are spelling.** `vecu` ≠ `vécu`. Grade them, but distinguish accent-only misses from
  real ones — that distinction is pedagogically the point.
- **The question mark is meaningful in the interrogatives drill.** The house rule is to trim
  trailing punctuation, but the intonation question (*Tu aimes ça ?*) is distinguished from the
  plain statement by the `?` alone — so that one form requires it, while *est-ce que* and inversion
  do not, being marked by word order. Trimming it there would have accepted a statement as a
  question.
- **Normalize forgivingly, then compare exactly.** Hyphens/spaces equivalent, curly quotes and
  apostrophes stripped, trailing punctuation and whitespace trimmed — trim *before* stripping
  trailing punctuation or `"sept . "` fails.
- **Never a CNAME at the apex domain.** GitHub Pages needs the four A records.

## Rejected, with reasons — don't re-propose without new information

- **Backend for cross-user progress** (DynamoDB or Cloudflare KV). The write endpoint was the
  only thing creating denial-of-wallet risk, and AWS Budgets refreshes ~3×/day so billing alerts
  can't act as a circuit breaker. Static hosting has nothing to hammer. The link-based export
  gives the tutor feedback loop without any of it.
- **IP-based user identification.** Static hosting can't see client IPs, IPs collide behind NAT
  and rotate on mobile, and they're personal data. An anonymous UUID was built and then removed
  as unnecessary once the backend was dropped.
- **Shared stylesheet.** Breaks constraint 2.

## Backlog

- **Interrogatives — built** (`/interrogatives/`), yes/no questions only. Still open: question
  words (*où / quand / comment / pourquoi / combien / que*), which are harder because the accepted
  set differs per word — *Tu vas où ?* is fine in situ but *Tu viens pourquoi ?* is marginal, and
  *que* becomes *quoi* in situ (*Tu fais quoi ?*). Also open: noun subjects, which need a resumptive
  pronoun in the inversion (*Marie aime-t-elle ça ?*), and negative questions.
- **Futur simple** on the same 70 verbs. Endings are uniform; the content is irregular stems
  (*aller→ir-*, *voir→verr-*, *vouloir→voudr-*, *venir→viendr-*). Those stems are also the
  conditional, so one drill covers two tenses.
- More reflexive verbs added to the passé composé drill rather than a separate reflexives page —
  the existing drill already covers six with agreement.

## Source

Passé composé verb list from *Le passé composé · participes passés* by Marine Vuigner, FLE
Nantes. Two typos on that sheet are corrected in the drill: `demander → demandé` and
`rentrer → rentré`. Also note **monter, descendre, sortir, rentrer, retourner** are listed as
*être* verbs but take *avoir* with a direct object.