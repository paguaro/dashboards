# Fire Risk Dashboards — v4.0
Repository: paguaro/dashboards
Generated: 20260320

## URLs (GitHub Pages)
- Landing:   https://paguaro.github.io/dashboards/
- Portugal:  https://paguaro.github.io/dashboards/portugal/
- Sardegna:  https://paguaro.github.io/dashboards/sardegna/
- Mobile:    https://paguaro.github.io/dashboards/mobile/

## Struttura
```
dashboards/
├── .nojekyll
├── index.html                    ← landing page (3 card: PT · SA · 📱)
├── portugal/
│   ├── index.html                ← dashboard PT v4.0
│   ├── .nojekyll
│   └── backup/dashboard_risco_fogo_v4_20260320.html
├── sardegna/
│   ├── index.html                ← dashboard SA v4.0 SARD
│   ├── .nojekyll
│   └── backup/dashboard_sardegna_v4_20260320.html
└── mobile/
    ├── index.html                ← mobile v1.0
    ├── .nojekyll
    └── backup/mobile_v1_20260320.html
```

---

## Dashboard Portugal — v4.0
**Stack:** HTML single-file · Leaflet · Plotly 2.35 · Open-Meteo API · ICNF ArcGIS REST

### Funcionalidades principais
- **HDW_eff** — Hot-Dry-Windy index com ponderação 70/30 gust (Srock et al. 2018, estendido)
- **HDWc** — acumulador sazonal com decaimento α mensal, calibrado OOS · AUC 0.9718/0.9679
- **Alpha Preset Manager v4.0** — 5 regiões predefinidas + input custom 12 valores CSV
- **Previsão 16 dias** — Open-Meteo (ICON-EU / IFS HRES / GFS) + warmup ERA5 21 dias
- **Seção 72h** — slider janela temporal (1d/2d/3d), 4 gráficos, barbelas meteorológicas, rosa dos ventos com estatísticas
- **ICNF GIFR 1975–2022** — query layers 0+2 em paralelo (sobreposições + retorno), perímetros BDG 1975–2024
- **Similaridade histórica** — pré-condicionamento 90 dias, ranking por cosseno, 10 features
- **Export PDF** — inclui seção 72h + preset α ativo
- **Anotações** em todos os gráficos, hover unificado, fullscreen

### Alpha Presets (fonte bibliográfica)
| Preset | Território | Fonte |
|---|---|---|
| 🇵🇹 Portugal | ANEPC calibrado | OOS AUC 0.97 · 2014–2025 |
| 🇺🇸 California | Siccità strutturale mag–nov | Keeley & Syphard 2016 · Abatzoglou et al. 2018 |
| 🇦🇺 Australia | Stagione fuochi australe ott–mar | Nolan et al. 2016 · McArthur 1967 FFDI |
| 🇨🇦 Canada | Stagione corta giu–ago | Stocks et al. 1998 · Canadian FWI |
| 🌍 Mediterranean | Europa mediterranea occidentale | Ruffault et al. 2020 · Turco et al. 2019 |

---

## Dashboard Sardegna — v4.0 SARD
**Stack:** identico PT · calibrazione CFVA 2005–2025 · AUC train=0.667 test=0.704

### Differenze rispetto a PT
- α mensile calibrato su dati CFVA Sardegna (profilo bimodale: picco giu + ott)
- Griglia storica CFVA 10km per query di prossimità
- Soglie HDWc calibrate su distribuzione sarda
- Senza BDG áreas ardidas (layer dedicato CFVA)
- Tutti gli altri moduli identici a PT (72h, similaridade, PDF, annotazioni)

---

## Mobile — v1.0 beta
**Stack:** HTML single-file · Leaflet · Plotly · Open-Meteo · ICNF ArcGIS REST
**Target:** smartphone/tablet, touch-first, orientamento portrait

### Funcionalidades
- **Mappa touch** — tap per selezionare punto, marker arancione
- **GPS** 📍 — `watchPosition` con `enableHighAccuracy`, carica punto automaticamente
- **4 tab grafici** — 🌡 T & HR · 🔥 HDW_eff + barbule · 💧 Precipitação · 🌬 Rosa dos ventos
- **Selettore finestra** — 1d / 2d / 3d con preset button
- **Risk strip** — semaforo rischio + HDWc + picco previsto + metriche T/HR/vento/direzione/HDW max
- **Bottom sheet** ☰ — scorre dal basso, contiene:
  - Dati ICNF GIFR (retorno, anos fogo, probabilidade, chips anni)
  - Perímetros BDG (lista incêndios con ha, duração, causa, distrito)
  - Toggle layer sobreposições ICNF
  - Toggle layer perímetros BDG
- **α fisso Portugal** (ANEPC 2014–2025)
- **localStorage condiviso** con dashboard desktop (stesso punto al prossimo avvio)
- **ERA5 warmup** 21 dias com timeout 8s (non-blocking)

### Limitações v1.0
- Alpha selector non disponível (solo Portugal)
- No export PDF
- No similaridade histórica
- No anotações
- GPS richiede HTTPS (non funziona su `file://` locale)

---

## Arquitetura técnica

### API calls per punto (PT/SA desktop)
1. Open-Meteo `/v1/forecast` daily — 16 giorni
2. Open-Meteo `/v1/forecast` hourly — 384h
3. ERA5 `/v1/archive` — 21 giorni warmup
4. ERA5 `/v1/archive` × N anni — similaridade 90 giorni
5. ICNF GIFR layers 0+2 — sobreposições + retorno
6. BDG areas_ardidas — 19 layers paralleli (1975–2024)

### Modello HDW_eff
```
g_eff = max(gust, wind)          // vincolo fisico
v_eff = 0.7×wind + 0.3×g_eff    // ponderazione conservativa
HDW   = VPD(T,HR) × v_eff[m/s]  // formula Srock et al. 2018
HDWc  = HDW + α[mese] × HDWc_ieri  // accumulo sazonal
```

### Deploy
Tutti i file sono HTML single-file — nessuna dipendenza locale.
Il `.nojekyll` disabilita il processore Jekyll di GitHub Pages.

---

## Autore
**Giuseppe Cornaglia** — ANEPC · Copernicus EMS National Focal Point Portugal
Colaboração científica: Marco Turco (npj Natural Hazards 2026)
