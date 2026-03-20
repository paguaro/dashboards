# Fire Risk Dashboards — v4.0
Repository: dashboards
Generated: 20260320

## URLs (GitHub Pages)
- Landing: https://[user].github.io/dashboards/
- Portugal: https://[user].github.io/dashboards/portugal/
- Sardegna: https://[user].github.io/dashboards/sardegna/

## Struttura
```
dashboards/
├── .nojekyll
├── index.html          ← landing page
├── portugal/
│   ├── index.html      ← dashboard PT v4.0
│   ├── .nojekyll
│   └── backup/dashboard_risco_fogo_v4_20260320.html
└── sardegna/
    ├── index.html      ← dashboard SA v4.0 SARD
    ├── .nojekyll
    └── backup/dashboard_sardegna_v4_20260320.html
```

## Deploy GitHub Pages
1. Crea repository pubblico `dashboards`
2. Carica tutti i file mantenendo la struttura cartelle
3. Settings → Pages → Branch: main / (root)
4. Il .nojekyll disabilita Jekyll — necessario per file HTML diretti

## Novità v4.0
- Alpha Preset Manager: Portugal / California / Australia / Canada / Mediterranean
- Custom α input (12 valori CSV)
- Sezione 72h completa con slider, barbule, rosa con statistiche
- Tabelle chiuse all'apertura
- Export PDF con sezione 72h + preset α attivo

## Autore
Giuseppe Cornaglia — ANEPC / Copernicus EMS NFP Portugal
