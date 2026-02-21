# 🇳🇱 Woordenschat Quiz

A mobile-friendly multiple-choice vocabulary quiz for Dutch learners at **B1–B2 level**. Instead of showing translations, learners infer meaning from **contextual example sentences** — the same V1 UX pattern developed for maximum acquisition depth.

**Built as a companion to [Werkwoorden Quiz](https://github.com/chardrizard/samengestelde-werkwoorden-quiz)** — same design system, same architecture.

---

## What makes this different?

Most vocabulary apps show translations immediately. This quiz shows **3 example sentences in context** first — no English, no definitions. You infer the meaning, then choose. Feedback explains *why*, not just what's correct.

This mirrors how you'd encounter a word while reading authentic Dutch content.

---

## Themes (Hoofdstukken)

| Theme | Words |
|-------|-------|
| ✈️ **Reizen & Vervoer** | vertrekhal · douane · overstappen · handbagage · ... |
| 📚 **School & Onderwijs** | beoordelen · rooster · herkansen · slagen · stage · ... |
| 🌍 **Aardrijkskunde** | bevolkingsdichtheid · klimaatzone · urbanisatie · delta · ... |
| 💼 **Werk & Carrière** | solliciteren · vergadering · proeftijd · pensioen · ... |
| 🏥 **Gezondheid & Lichaam** | symptoom · diagnose · vaccinatie · chronisch · ... |

---

## Architecture

```
woordenschat-quiz/
├── index.html              ← UI (HTML + CSS + JS, fetches from data/)
├── site.webmanifest        ← PWA manifest
├── data/
│   ├── themes.json         ← Theme registry (id, label, emoji, color, wordCount)
│   ├── reizen.json         ← Questions for Reizen theme
│   ├── school.json         ← Questions for School theme
│   ├── aardrijkskunde.json ← Questions for Aardrijkskunde theme
│   ├── werk.json           ← Questions for Werk theme
│   └── gezondheid.json     ← Questions for Gezondheid theme
├── assets/
│   └── icons/              ← Favicons and PWA icons
├── README.md
├── LICENSE
└── .gitignore
```

**Architecture supports up to 2000+ words** across unlimited themes. The UI fetches `themes.json` on startup, then lazily loads each theme's question file on demand. Progress is stored in `localStorage`.

---

## Question format

Each question in a theme JSON file:

```json
{
  "word": "overstappen",
  "pos": "werkwoord · scheidbaar",
  "examples": [
    "We moesten in Frankfurt <mark>overstappen</mark> voor onze vlucht naar Bangkok.",
    "Hij stapte in Brussel <mark>over</mark> op een andere trein.",
    "Bij <mark>overstappen</mark> heb je soms maar twintig minuten."
  ],
  "options": [
    "je bagage checken",
    "van vervoersmiddel wisselen tijdens een reis",
    "inchecken op de luchthaven",
    "een ticket omboeken"
  ],
  "correct": 1,
  "explanation": "'Overstappen' betekent tijdens een reis wisselen van vliegtuig, trein of bus.",
  "hint": "Je bent al onderweg maar moet ergens tussenstop maken en een ander vervoer nemen."
}
```

- `word` — the target word shown as the question
- `pos` — part of speech label (shown below the word)
- `examples` — 1–3 context sentences. Use `<mark>` to highlight the target word. **No translations.**
- `options` — 4 answer choices (all in Dutch)
- `correct` — 0-based index of the correct option
- `explanation` — shown in feedback after answering (Dutch)
- `hint` — optional hint (Dutch, shown on demand)

---

## Adding a new theme

1. Create `data/themename.json` with an array of questions (see format above)
2. Add to `data/themes.json`:

```json
{
  "id": "themename",
  "label": "Thema Naam",
  "description": "woord1 · woord2 · woord3",
  "emoji": "🎯",
  "color": "#FF8C42",
  "wordCount": 10
}
```

3. Push — GitHub Pages deploys automatically.

`wordCount` should match the actual number of questions in your JSON file. It drives the progress ring on the home screen.

---

## Progress tracking

Progress is saved in `localStorage` under the key `wq_progress_v1`. It tracks:

- Which words have been **seen**
- Which words have been answered **correctly** (wrong resets the word — forces re-learning)
- **Best score** per theme
- **Attempts** per theme

The progress ring on the home screen shows `correct unique words / total wordCount`. Resetting progress clears `localStorage`.

---

## Scaling to 2000+ words

The architecture is designed for this from the start:

- Each theme is a separate JSON file — browser caches independently
- `themes.json` is the only file loaded on startup (small)
- Questions are lazy-loaded per theme, only when needed
- Each session picks a **random subset** — so 200 words in a theme feels fresh every time
- `localStorage` progress schema uses `Set` for O(1) word lookups

To add 2000 words: create 20 themes of 100 words each, or 10 themes of 200 words each. No code changes required.

---

## Session counts

The count selector lets users choose:
- **10 vragen** — quick session (~8 min)
- **20 vragen** — standard session (~15 min)
- **Alles** — full theme (varies)

Questions are randomly shuffled each session, so the same theme stays fresh across multiple attempts.

---

## Design system

Uses the same tokens as [Werkwoorden Quiz](https://github.com/chardrizard/samengestelde-werkwoorden-quiz):

- `DM Sans` + `DM Mono` typography
- Dark surface stack: `#0F1117` → `#181A20` → `#1E2028` → `#252830`
- Semantic colors: correct (green), wrong (red), per-theme accent
- Per-theme accent color for progress ring and navigation
- Mobile-first, 480px max-width, safe-area padding for iOS

---

## Run locally

```bash
git clone https://github.com/YOUR_USERNAME/woordenschat-quiz.git
cd woordenschat-quiz
python3 -m http.server 8000
# Visit http://localhost:8000
```

> A local server is required because of `fetch()` — opening `file://` won't work.

---

## Roadmap

- [ ] More themes: Politiek, Technologie, Natuur & Milieu, Wonen, Familie
- [ ] Spaced repetition mode (SRS algorithm for wrong answers)
- [ ] Mix mode — random words from all themes
- [ ] Streak tracking
- [ ] PWA install-to-homescreen
- [ ] Custom domain

---

## Contributing

Wrong explanation? Unnatural sentence? Edit the relevant JSON in `data/` and open a PR. Native speaker corrections especially welcome.

## License

MIT — use it, fork it, adapt it.

---

*Built for the Dutch diaspora community 🧡*
