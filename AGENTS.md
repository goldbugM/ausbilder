# AGENTS.md — ausbilder (AEVO Meister)

## Projekt
Single-page AEVO-Lernapp (index.html, ~24.7k Zeilen, Tailwind CDN, Vanilla JS).
Deploy: Vercel (project: ausbilder) + GitHub goldbugM/ausbilder.

## Struktur
- `index.html` — komplette App (Lernen/Üben/Prüfung/Ergebnisse/Geführt)
- `manifest.json`, `icon-*.png`, `apple-touch-icon.png`, `favicon-32.png` — PWA-Assets
- Views: #view-learn, #view-practice, #view-exam, #view-results, #view-guided
- View-Switch: `switchMode(mode)` —顶部 Nav-Buttons (#nav-btn-*) + Mobile-Tabbar (#mobile-tabbar [data-tab])


## Theme-System (seit 2026-09-20, Commit 21799cb)
- Alle Farben laufen ueber CSS-Tokens `var(--c-*)` — :root = Light, `[data-theme="dark"]` = Void-Dark
- Toggle: `toggleTheme()`/`applyTheme(t)` + Button `#theme-toggle-btn` im Header
- Persistenz: localStorage `aevo-theme` > `?theme=dark|light` URL-Param > prefers-color-scheme (Anti-FOUC Head-Script)
- Neue Farben IMMER als var(--c-token) in BEIDEN Bloecken (:root + dark) definieren, nie als rohen Hex

## Konventionen
- Light-Theme (seit 2026-09-20): Page-BG #f6f8fb, Cards #ffffff, Ink #17202e, Border #dde2ea, Akzent #ec4a1d
- Mobile: Bottom-Tab-Bar (md:hidden), safe-area padding via env(), PWA standalone
- Farben als Tailwind-Arbitrary-Values UND in JS-Template-Strings → Änderungen immer global suchen

## Frage-Quellen (Stand 2026-09-21)
- IDs 1-415: eigene DIHK-naeher Fragen (HF1-4)
- IDs 416-495: Feldhaus Pruefungs-Check Original-Satz 1 (80)
- IDs 496-655: Feldhaus Pruefungs-Check Saetze 2-3 (160)
- IDs 656-695: IHK Musteraufgabensatz Satz 66 (49)
- IDs 696-775: fotografierte IHK Original-Pruefung (80, KI-verifiziert via Claude Opus + Gemini)
- Filter/Quellen: 'pc', 'pc1', 'ihk66', 'ihkf' in practice + exam-source Dropdowns
- OCR-Pipeline: MiniMax Vision braucht max_tokens>=12000 (reasoning frisst Tokens); foto-basierte Exam-Extraktion in /a0/usr/workdir/projects/2026-09-21_pruefung-zip/

## Icon-System (2026-09-21)
- Alle bunten Emojis ersetzt durch Inline-SVG-Sprite (16 Symbole, Feather-Stil, currentColor): i-book, i-target, i-clipboard, i-compass, i-moon, i-sun, i-shuffle, i-network, i-bulb, i-search, i-check-c, i-alert, i-award, i-x-c, i-pause, i-play.
- Verwendung: `<svg class="icn" aria-hidden="true"><use href="#i-book"/></svg>` (skaliert via CSS 1em).
- JS-Icon-Swaps (Theme-Toggle, Timer) nutzen innerHTML statt textContent (SVG-Markup).
- Typografische Glyphen (→ ← ✓ ✗ ⚑ ⚐ ⚠ ▲ ▼) bewusst erhalten.
- Neue Icons IMMER als symbol ins Sprite + via use referenzieren, nie als Emoji zurückbauen.
