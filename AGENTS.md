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

## Denkfallen-Engine (2026-09-29, Commit fa2cc38)
- Regelbasierte Distraktor-Analyse: `AEVO_TRAPS` (6 Fallen-Typen) + `detectTrapsForAnswer()` / `detectTrapRadar()` in index.html (vor AEVO_QUESTIONS injiziert)
- Fallen-Typen: jahrbindung (Ausbildungsjahr-Koeder), absolutismus (immer/nie), rollenkonflikt (Kontrolleur vs. Berater + missedPattern fuer uebersehene Berater-Loesung), verneinung (nicht/kein Kipper), zahlenkoeder (Fake-Fristen), mussfalle (Pseudo-Pflicht bei Methodenfreiheit)
- Jede Falle: name, patterns[] (Regex), lure (Verfuehrungs-Psychologie), logic (Pruefungslogik), merk (Merksatz)
- UI-Integration: (1) Fallen-Radar VOR Antwort im Practice-View (einklappbar via toggleTrapRadar), (2) renderTrapAnalysisBlock() NACH falscher Antwort in Practice + Exam-Review + beiden Topic-Quiz-Funktionen (checkTopicQuiz/checkTopicQuizPc)
- Rollen-Blindheit-Heuristik: verpasste Berater-Option + gewaehlte Kontroll-/Jahres-/Muss-Option -> zusaetzlicher Fallback-Hit
- Neue Fallen IMMER in AEVO_TRAPS mit id/name/patterns/lure/logic/merk anlegen; Test: Engine-Simulation via node mit extrahiertem Script-Block
- v1.1 (Commit 57694ac): +7 Fallen aus Distraktor-Analyse (2448 Distraktoren aller 775 Fragen): anspruchsmythos, fantasyinst, pflichtverweigerung, lernzieldreh, altersmythos, sofortaktion, verguetungsmythos — alle Precision-geprueft (80-100% Trefferquote nur in falschen Optionen)
- radarOk: false Flag bei unpraezisen Fallen (verneinung, zahlenkoeder, rollenkonflikt, mussfalle) = nur Post-Antwort-Analyse, kein praeventiver Radar; Radar-Rate 22% gesamt / 18% ihkf
- Analyse-Pipeline: Distraktoren via Bracket-Counting aus AEVO_QUESTIONS extrahieren, gegen Kandidaten-Regex testen, Precision = Treffer(falsch)/(Treffer falsch+richtig), nur >=80% uebernehmen
