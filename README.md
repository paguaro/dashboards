# Fire Risk Dashboards
Repository: paguaro/dashboards
Updated: 20260320

## URLs (GitHub Pages)
- Landing:    https://paguaro.github.io/dashboards/
- Portugal:   https://paguaro.github.io/dashboards/portugal/
- Sardegna:   https://paguaro.github.io/dashboards/sardegna/
- FireMeteo:  https://paguaro.github.io/dashboards/firemeteo/
- Mobile:     https://paguaro.github.io/dashboards/mobile/

## Structure
```
dashboards/
├── .nojekyll
├── index.html                  ← landing page (PT · SA · 🌐 · 📱)
├── README.md
├── portugal/   index.html      ← PT v4.0
├── sardegna/   index.html      ← Sardinia v4.0 SARD
├── firemeteo/  index.html      ← FireMeteo v5.0
└── mobile/     index.html      ← Mobile v1.0
```

---

## Portugal — v4.0
HDW_eff · HDWc OOS AUC 0.97 · α presets · ICNF GIFR + BDG · 72h · Similarity · PDF

## Sardinia — v4.0 SARD
Same engine as PT · CFVA calibration · bimodal α (Jun+Oct)

## FireMeteo — v5.0 🌐
**Language:** English · Global coverage

### New features vs PT/SA
- 🗺 **Basemap selector** — Voyager · Satellite · OSM · Terrain · Dark Matter
- 🔥 **EFFIS WMS** — year selector 2003→present, GetFeatureInfo popup on click
- 🔧 **Custom WFS/GeoJSON** — auto-detect (WFS OGC / ArcGIS REST / GeoJSON), attribute popup, localStorage
- ★ **Named α presets** — save custom configurations with a name
- 🇬🇷 **Greece preset** added
- All UI in English

### α Presets
| Preset | Source |
|---|---|
| 🇵🇹 Portugal | ANEPC 2014–2025 · AUC 0.97 |
| 🇺🇸 California | Keeley & Syphard 2016 |
| 🇦🇺 Australia | Nolan et al. 2016 · McArthur FFDI |
| 🇨🇦 Canada | Stocks et al. 1998 · FWI |
| 🌍 Mediterranean | Ruffault et al. 2020 |
| 🇬🇷 Greece | Karali et al. 2023 |

## Mobile — v1.0 📱
Touch-first · GPS · 4 tabs · Bottom sheet · ICNF + BDG · ERA5 warmup

---

## HDW_eff Model
```
v_eff = 0.7×wind + 0.3×max(gust,wind)
HDW   = VPD(T,RH) × v_eff[m/s]
HDWc  = HDW + α[month] × HDWc_prev
```

## Chart Standards (all dashboards)
- Wind barbs: HDW chart only · BARB_Y_FRAC=0.22
- Gust: 2.5px solid purple · Wind mean: 2px dotted blue

## Author
Giuseppe Cornaglia · ANEPC · Copernicus EMS NFP Portugal
