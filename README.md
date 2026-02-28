# 🇳🇱 Woordenschat Quiz

A mobile-friendly, context-first vocabulary quiz for Dutch learners at **B1–B2 level**. No translations — you infer meaning from **contextual example sentences**, then choose the correct Dutch definition.

**Built as a companion to [Werkwoorden Quiz](https://github.com/chardrizard/samengestelde-werkwoorden-quiz)** — same design system, same interaction patterns.

---

## Live demo

→ **[Open the quiz](https://chardrizard.github.io/woordenschat-B1-quiz/)**

---

## What makes this different?

Most vocabulary apps show translations immediately. This quiz shows **example sentences in context** first — no English, no definitions. You infer the meaning, then choose. Feedback explains *why*, not just what's correct.

Difficulty is calibrated using the **Hazenberg & Hulstijn (2002)** frequency framework — the same list used by Dutch integration exams. Every word is tagged to a frequency level:

| Level | Frequency band | Label |
|-------|---------------|-------|
| N1 | Words 1–500 | Basis |
| N2 | Words 501–1000 | Gemiddeld |
| N3 | Words 1001–1500 | Gevorderd |
| N4 | Words 1501–2000 | Expert |

---

## Themes (Hoofdstukken)

| Theme | Words |
|-------|-------|
| ✈️ **Reizen & Vervoer** | verblijf · bestemming · douane · overstappen · toeslag · ... |
| 📚 **School & Onderwijs** | beoordelen · rooster · slagen · stage · herkansen · scriptie · ... |
| 🌍 **Aardrijkskunde** | migratie · zeeniveau · delta · urbanisatie · evenaar · ... |
| 💼 **Werk & Carrière** | solliciteren · vergadering · leidinggevende · proeftijd · ... |
| 🏥 **Gezondheid & Lichaam** | symptoom · herstel · diagnose · chronisch · bijwerking · ... |

---

## Architecture

```
woordenschat-quiz/
├── index.html          ← The entire app (HTML + CSS + JS + all data, self-contained)
├── site.webmanifest    ← PWA manifest (installable on mobile)
├── assets/
│   └── icons/          ← Favicons and PWA icons (192px, 512px)
├── README.md
├── LICENSE
└── .gitignore
```

**Everything is embedded in `index.html`.** No build step, no server, no external dependencies. Works as `file://`, GitHub Pages, or any static host.

---

## Question format (embedded in index.html)

Each question object inside `const QUESTIONS`:

```js
{
  word: "overstappen",
  pos: "werkwoord · scheidbaar",
  level: 2,                       // Hazenberg & Hulstijn frequency level (1–4)
  examples: [
    "We moesten in Frankfurt <mark>overstappen</mark> voor onze vlucht naar Bangkok.",
    "Hij stapte in Brussel <mark>over</mark> op een andere trein.",
    "Bij <mark>overstappen</mark> heb je soms maar twintig minuten."
  ],
  options: [
    "je bagage checken",
    "van vervoersmiddel wisselen tijdens een reis",   // ← correct (index 1)
    "inchecken op de luchthaven",
    "een ticket omboeken"
  ],
  correct: 1,
  explanation: "'Overstappen' betekent tijdens een reis wisselen van vliegtuig, trein of bus.",
  hint: "Je bent al onderweg maar moet ergens tussenstop maken en een ander vervoer nemen."
}
```

| Field | Description |
|-------|-------------|
| `word` | Target word shown as the question |
| `pos` | Part of speech (shown below the word) |
| `level` | Frequency level 1–4 (Hazenberg & Hulstijn) |
| `examples` | 1–3 context sentences. Use `<mark>` to highlight the target word. No translations. |
| `options` | 4 answer choices (all Dutch) |
| `correct` | 0-based index of the correct option |
| `explanation` | Shown in feedback after answering (Dutch) |
| `hint` | Optional hint shown on demand (Dutch) |

---

## Adding words or themes

All data lives inside `index.html` in the `const QUESTIONS` object and `const THEMES` array.

### Add words to an existing theme

Find the theme key in `QUESTIONS` (e.g. `reizen:`) and append a new object:

```js
{ word: "inchecken", pos: "werkwoord", level: 1,
  examples: ["We konden online <mark>inchecken</mark> voor onze vlucht.", ...],
  options: ["je bagage ophalen", "je reis registreren voor vertrek", ...],
  correct: 1,
  explanation: "...",
  hint: "..." },
```

### Add a new theme

1. Add an entry to `const THEMES`:

```js
{ id:"politiek", label:"Politiek & Bestuur", emoji:"🏛️", color:"#FF8C42",
  description:"parlement · minister · wet · stemmen" },
```

2. Add a matching key in `const QUESTIONS`:

```js
politiek: [
  { word:"parlement", pos:"zelfstandig naamwoord · het", level:1, ... },
  ...
]
```

No other code changes needed. The theme card and progress ring appear automatically.

---

## Level filtering (Hazenberg & Hulstijn)

On the count-select screen, users choose a frequency level before starting. The quiz then filters the question pool to only that level. Each question carries a `level` field (1–4). The UI shows live word counts per level per theme.

**Design decision:** Level filtering happens at session start — not during the quiz. This keeps the experience clean and predictable.

---

## Progress tracking

Progress is saved in `localStorage` under the key `wq_progress_v1`. It tracks:

- Which words have been **seen**
- Which words have been answered **correctly** (a wrong answer *removes* the word from correct — forces re-learning)
- **Best score** per theme
- **Attempts** per theme

The progress ring on the home screen shows `correct unique words / total words in theme`. Resetting clears `localStorage`.

---

## Scaling to 2000+ words

The self-contained architecture scales cleanly:

```js
const QUESTIONS = {
  reizen:         [ /* up to 200 words */ ],
  school:         [ /* up to 200 words */ ],
  politiek:       [ /* new theme */       ],
  technologie:    [ /* new theme */       ],
  // ... as many themes as needed
};
```

- Random shuffle each session → same theme stays fresh across attempts
- `localStorage` Set-based lookup is O(1) regardless of vocabulary size
- Level filtering means learners can focus on exactly the words they need
- Browser caches the single HTML file — subsequent loads are instant

---

## Run locally

Just open `index.html` directly in a browser — no server needed.

```bash
git clone https://github.com/chardrizard/woordenschat-quiz.git
open index.html   # macOS
# or double-click index.html on Windows/Linux
```

> Unlike the previous version, this build requires **no local server** — there are no `fetch()` calls.

---

## Deploy to GitHub Pages

1. Push `index.html` (and `site.webmanifest`, `assets/`) to your repo
2. Go to **Settings → Pages → Source: Deploy from branch → main / root**
3. Done — live at `https://chardrizard.github.io/woordenschat-quiz/`

---

## Keyboard shortcuts

| Key | Action |
|-----|--------|
| `A` `B` `C` `D` or `1` `2` `3` `4` | Select answer |
| `Enter` | Next question |
| `H` | Toggle hint |
| `Esc` | Back / close |
| `↑` `↓` | Navigate options or theme cards |

---

## Roadmap

- [ ] More themes: Politiek, Technologie, Natuur & Milieu, Wonen, Familie
- [ ] Expand to 200 words per theme (2000 total)
- [ ] Spaced repetition mode (SRS for wrong answers)
- [ ] Mix mode — random words from all themes
- [ ] Streak tracking
- [ ] PWA install-to-homescreen (manifest + service worker)
- [ ] Custom domain

---

## Contributing

Wrong explanation? Unnatural sentence? Open the relevant section in `index.html` and edit the question object directly, then open a PR. Native speaker corrections especially welcome.

## License

MIT — use it, fork it, adapt it.

---

*Built for the Dutch diaspora community 🧡*
