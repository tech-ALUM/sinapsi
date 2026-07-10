---
title: Merge repo Savyt — riepilogo sessione 2026-07-08
type: note
permalink: sinapsi/bm-remember/merge-repo-savyt-riepilogo-sessione-2026-07-08
tags:
- manual-capture
- savyt
- git
- monorepo
---

# Merge repo Savyt — riepilogo sessione 2026-07-08

Richiesta: fondere le 4 repository dell'app in un'unica repo `savyt`, documentata, standardizzata e pronta per lavoro in parallelo su branch.

## Cosa è stato fatto

- Clonate le 4 repo `tech-ALUM` (codeDev, appDocumentation, savytScan, appDevRepo) in `~/Documents/ALUM/code/` con l'alias SSH `github.com-alum` (identità `albertoboffi-ALUM`).
- Monorepo costruito con `git-filter-repo --to-subdirectory-filter` per repo + `git merge --allow-unrelated-histories`: tutti gli 89 commit originali preservati (hash riscritti), `git log --follow` funziona su tutta la storia.
- Layout: `code/` (ex codeDev: app Flutter, backend Supabase, connettori, tools, PROGRESS.log) · `docs/` (ex appDocumentation: LaTeX ST, CF, PRE, INT, TN-DB, TN-UI, TN-OPT) · `scan/` (ex savytScan, PWA crowdsourcing) · `survey/` (ex appDevRepo, post-processing form Tally).

## Standardizzazioni

- `survey/`: `00. Answers` → `answers/`, `01. Post-processing` → `postProcessing/`; percorso `dataFolder` aggiornato in `mainFormProcesser.py`; `README.txt` convertito in Markdown con entry point corretto (`mainFormProcesser.py`, non `main.py`).
- `codeDev/webapp/` era copia identica di savytScan → eliminata; unica copia in `scan/`.
- Rimossi `.DS_Store`/`__pycache__` tracciati; `.gitattributes` unico a root; `.gitignore` root + LaTeX in `docs/`.
- `README.md` di root (mappa repo, provenienza, come usare ogni parte, indice documenti LaTeX) e `CONTRIBUTING.md` promosso a root con convenzione branch `<tipo>/<area>-<slug>` (area ∈ code|docs|scan|survey) per lavoro parallelo senza conflitti.

## Decisioni e stato finale

- Casa canonica: **`tech-ALUM/savyt`** (creata sotto albertoboffi-ALUM perché i collaboratori non possono creare repo nell'org, poi trasferita; il trasferimento ha rotto la vecchia redirect di rinomina savyt→codeDev). HEAD main = `fd521ab`, 96 commit.
- Le 4 repo originali sono **archiviate** su GitHub (leggibili, non scrivibili).
- **Protezione branch su main volutamente NON applicata** — scelta del team, non riproporre.
- Cloni locali delle 4 repo originali eliminati (erano puliti); in `~/Documents/ALUM/code/` resta solo `savyt/`, con `origin` = `git@github.com-alum:tech-ALUM/savyt.git`, in sync.

## Riferimenti

- Repo: https://github.com/tech-ALUM/savyt
- Log di sviluppo: `code/PROGRESS.log` (focus attuale: robustezza definizione prodotti nel DB cataloghi)
- Team: Paolo, Alberto, Tommaso, Francesco (Francesco merge le PR)