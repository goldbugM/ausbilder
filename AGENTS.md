# AGENTS.md — ausbilder (AEVO Meister)

## Projekt
Single-page AEVO-Lernapp (index.html, ~24.7k Zeilen, Tailwind CDN, Vanilla JS).
Deploy: Vercel (project: ausbilder) + GitHub goldbugM/ausbilder.

## Struktur
- `index.html` — komplette App (Lernen/Üben/Prüfung/Ergebnisse/Geführt)
- `manifest.json`, `icon-*.png`, `apple-touch-icon.png`, `favicon-32.png` — PWA-Assets
- Views: #view-learn, #view-practice, #view-exam, #view-results, #view-guided
- View-Switch: `switchMode(mode)` —顶部 Nav-Buttons (#nav-btn-*) + Mobile-Tabbar (#mobile-tabbar [data-tab])

## Konventionen
- Light-Theme (seit 2026-09-20): Page-BG #f6f8fb, Cards #ffffff, Ink #17202e, Border #dde2ea, Akzent #ec4a1d
- Mobile: Bottom-Tab-Bar (md:hidden), safe-area padding via env(), PWA standalone
- Farben als Tailwind-Arbitrary-Values UND in JS-Template-Strings → Änderungen immer global suchen
