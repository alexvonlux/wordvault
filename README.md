# Word Vault — The Hobbit English–Russian Dictionary

> A personal vocabulary study tool built from the complete text of *The Hobbit* by J.R.R. Tolkien.  
> Every unique word in the book — lemmatized, translated, and shown in context.

**Live site:** [alexvonlux.github.io/wordvault](https://alexvonlux.github.io/wordvault) *(password protected)*

---

## Features

- **4,086 unique words** extracted from the full text of *The Hobbit*, reduced to base (lemma) forms
- **English → Russian translations** via a hand-crafted dictionary of ~5,000+ entries
- **Book example sentences** — one per card, sourced directly from the text
- **Click-to-expand examples** — click the frequency badge on any card to see all occurrences of that word in the book (up to 10 sentences), with the word highlighted
- **Word frequency badges** with colour-coded tiers (rare → very common)
- **Alphabet filter** — jump to any letter section instantly
- **Full-text search** across all words
- **Delete cards** — remove words you already know; deletions persist across sessions via `localStorage`
- **AES-256 client-side encryption** — the entire dictionary is encrypted; a passphrase is required to unlock it
- **No server, no backend** — everything runs in the browser from a single static HTML file

---

## Technology Stack

### Data Pipeline (Python)

| Tool / Library | Purpose |
|---|---|
| `ebooklib` | Parse the `.epub` source file |
| `BeautifulSoup4` | Extract clean text from HTML chapters |
| Custom rule-based lemmatizer | Reduce 6,183 inflected tokens → 4,086 base forms (suffix stripping + 100+ irregular verb map) |
| Hand-crafted EN→RU dictionary (`ru_dict.py`, `ru_dict2.py`) | ~5,000 entries; no external API |
| `collections.Counter` | Word frequency counting mapped through the lemmatizer |
| `re`, `json` | Sentence extraction, data serialisation |

### Frontend

| Technology | Purpose |
|---|---|
| Vanilla HTML / CSS / JavaScript | Single-file app, zero dependencies at runtime |
| CSS custom properties + grid layout | Responsive card grid, theming |
| `localStorage` | Persist deleted cards across page reloads |
| Google Fonts — *Cinzel*, *IM Fell English* | Typography |

### Security & Hosting

| Tool | Purpose |
|---|---|
| [StatiCrypt](https://github.com/robinmoisson/staticrypt) v3.5.4 | AES-256 client-side encryption of the HTML file via `npx` |
| GitHub Pages | Free static hosting |
| Custom HTML login template | LoR-inspired password page (replaces StatiCrypt's default UI) |

---

## How It Works

```
The Hobbit .epub
      │
      ▼
ebooklib + BeautifulSoup  →  raw text (hobbit_text.txt)
      │
      ▼
Tokeniser + Lemmatizer    →  4,086 canonical base forms
      │
      ▼
EN→RU Dictionary lookup   →  translations per base form
      │
      ▼
Sentence extractor        →  example sentences per word (from book text)
      │
      ▼
HTML generator (Python)   →  hobbit_vocabulary_russian.html  (6.9 MB)
      │
      ▼
StatiCrypt                →  index.html  (AES-256 encrypted, ~15 MB)
      │
      ▼
GitHub Pages              →  live at alexvonlux.github.io/wordvault
```

---

## Project Structure

```
.
├── hobbit_vocabulary_russian.html   # Main dictionary (source, unencrypted)
├── index.html                       # Encrypted output — deployed to GitHub Pages
├── lor_template.html                # Custom StatiCrypt login page template
│
├── ru_dict.py                       # EN→RU dictionary, ~3,000 entries
├── ru_dict2.py                      # EN→RU dictionary supplement, ~1,300 entries
│
├── lemmatize.py                     # Rule-based English lemmatizer
├── build_cards.py                   # HTML card generator
├── build_final.py                   # Final assembly script
├── add_freq.py                      # Word frequency badge injection
│
├── canonical_words.json             # 4,086 base forms + inflected→base mapping
├── translations_final.json          # Word→Russian translation pairs
├── all_examples.json                # All book sentences per word (up to 10)
└── hobbit_text.txt                  # Full extracted book text
```

---

## Reproducing the Build

**Prerequisites:** Python 3.9+, Node.js 16+

```bash
# 1. Extract text from epub
python3 lemmatize.py

# 2. Build the HTML dictionary
python3 build_final.py

# 3. Encrypt with StatiCrypt
npx staticrypt hobbit_vocabulary_russian.html \
  --password 'YOUR_PASSWORD' \
  --no-remember \
  --template lor_template.html \
  --template-title "Word Vault" \
  --template-placeholder "Speak the passphrase…" \
  --template-button "Enter the Vault" \
  --template-error "The gates remain shut. Wrong passphrase." \
  --short

# 4. Deploy
cp encrypted/hobbit_vocabulary_russian.html index.html
# Push index.html to the root of your GitHub Pages branch
```

---

## Lemmatizer Notes

NLTK / WordNet were not available in the build environment, so a custom rule-based lemmatizer was written. It handles:

- Regular plural nouns (`-ies`, `-ves`, `-s`, `-es`)
- Regular verb conjugations (`-ed`, `-ing`, `-s`)
- Adverb → adjective reduction (`-ly`)
- 100+ irregular verbs (`went → go`, `was/were → be`, etc.)
- Common irregular nouns (`men → man`, `feet → foot`, etc.)

---

## Legal Notice

The source text of *The Hobbit* is copyright © The Tolkien Estate. This project uses excerpts solely for **personal, non-commercial educational purposes**. The dictionary and tooling code are original work.

---

## License

The **code** in this repository (Python scripts, HTML templates, CSS, JavaScript) is released under the [MIT License](LICENSE).

**Why MIT?**  
It is the most permissive and widely recognised open-source licence. It lets anyone use, copy, modify, and distribute the code with minimal restrictions, while keeping you free of liability. For a personal tool like this it is the standard sensible choice.

> **Note:** The *content* (book sentences, translations) is **not** covered by the MIT licence. The Hobbit text remains the property of the Tolkien Estate. Do not redistribute this project with the encrypted dictionary in a way that could constitute commercial use of the copyrighted text.

---

## Author

[alexvonlux](https://github.com/alexvonlux)
