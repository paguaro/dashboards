# Fire Risk Dashboards
Repository: paguaro/dashboards  
Updated: 2026-04-17

## Live URLs (GitHub Pages)
| Tool | URL | Version |
|------|-----|---------|
| Landing | https://paguaro.github.io/dashboards/ | — |
| FireMeteo | https://paguaro.github.io/dashboards/firemeteo/ | v42 |
| Portugal | https://paguaro.github.io/dashboards/portugal/ | v4 |
| Sardegna | https://paguaro.github.io/dashboards/sardegna/ | v6 |
| Mobile | https://paguaro.github.io/dashboards/mobile/ | v1 |
| WildWinds | https://paguaro.github.io/dashboards/wildwinds/ | v2 |
| HDWc Batch | https://paguaro.github.io/dashboards/batch/ | v29 |
| HDWc Builder | https://paguaro.github.io/dashboards/hdwc_builder/ | v8 |

## Repository Structure
```
dashboards/
├── index.html              ← landing page
├── README.md
├── firemeteo/index.html    ← FireMeteo v42 — global dashboard
├── portugal/index.html     ← Portugal v4 — ANEPC operational
├── sardegna/index.html     ← Sardegna v6 — CFVA operational
├── mobile/index.html       ← Mobile v1 — touch/GPS
├── wildwinds/index.html    ← WildWinds v2 — wind analysis
├── batch/index.html        ← HDWc Batch v29 — multi-fire analysis
└── hdwc_builder/index.html ← HDWc Builder v8 — engine config editor
```

---

## HDWc Engine v2

### Core formula
```
W_eff      = 0.7 × wind_mean + 0.3 × gust_max          [km/h]
VPD_h      = 0.6108 × exp(17.27T/(T+237.3)) × (1-RH/100) [hPa]
HDW_eff    = max(VPD_h × W_eff_h/3.6, h ∈ [10:00–17:00])
alpha_t    = alpha_ref(month) + Δdry + Δnight + Δrelief + Δfoehn
HDWc_t     = HDW_eff + alpha_t × (beta_t × HDWc_{t-1})
```

Note on `beta_t`: the washout factor is applied **only to the memory term** `HDWc_{t-1}`, not to the daily atmospheric event `HDW_eff`. Rationale: rain reduces fuel accumulation (memory), but does not suppress the atmospheric forcing of the day itself (wind and heat may coexist with rain).

### Dynamic alpha
- `alpha_ref`: monthly baseline, regional calibration (max jump ≤ 0.15/month)
- `Δdry`: streak of class-2/3 days ≥ streak_activation_days; logarithmic long-streak boost past `days_to_severe_stress`
- `Δnight`: night score — 4 tiers (cold / warm / tropical / severe) with VPD modulator (wet/dry/extreme thresholds derived from regional `RH_dry` / `RH_very_dry` / `RH_wilt`)
- `Δrelief`: rain ≥ rain_effective_mm
- `Δfoehn`: foehn wind match score × delta_alpha_per_score_unit
- `beta`: washout from rain (5 thresholds, intensity-weighted by rain_hours)
- `floor`: dynamic — `alpha_ref` on non-relief days, `0` on relief days (prevents low-alpha months from being swallowed by a hard floor)

### Day classification (strict AND for streak, OR for display)
| Class | Condition |
|-------|-----------|
| 0 | Rain ≥ rain_effective_mm |
| 1 | Neutral |
| 2 | Tmax ≥ Tmax_c2 AND HDW ≥ HDW_c2 |
| 3 | Tmax ≥ Tmax_c3 AND HDW ≥ HDW_c3 |

Vegetation stress modifier (applied after base classification): if `rhMean ≤ RH_wilt` and `HDW_eff ≥ 0.75·HDW_c2`, dc is raised to at least 2; if daytime `VPD ≥ VPD_stress`, dc is incremented by 1 (capped at 3).

### HDW_eff F_class amplifier
Non-linear boost applied when HDW_eff ≥ HDW_c2:
```
Fc = (HDW_eff - HDW_c2) / (HDW_c3 - HDW_c2)   [clamped to 1 at HDW_c3]
k  = k_base | k_mid | k_high   (selected by Tmax tier)
if drel > delta_rel_threshold:  Fc *= (1 + drel)
HDW_eff_amp = HDW_eff + k × Fc
```

---

## Dashboards

### FireMeteo — v42 🌐
Global wildfire risk monitoring with ERA5 archive + 16-day forecast.
- HDWc v2 engine, α dynamic, geo-detection via bbox
- Multi-zone JSON packages (portugal_package_v2, mediterranean_megafire_package_v2)
- EFFIS WMS + VIIRS hotspots, custom WFS/GeoJSON/ArcGIS REST layers
- Wind barbs, wind shifts ⚡, foehn ♦, tropical nights NS
- **Drill-down 72h**: inspect any ERA5 period hour-by-hour
- PDF export, CSV filtered export, event markers

### Portugal — v4 🇵🇹
Operational dashboard for ANEPC with 11 calibrated ecoregions.
- Embedded portugal_package_v2.json (11 zones, bbox geo-detection)
- Foehn: Norte (NNW→NNE) + Levante per zone
- Same features as FireMeteo v42 including drill-down 72h

### Sardegna — v6 🇮🇹
Operational dashboard for CFVA with full ERA5 archive pipeline.
- Foehn: Maestrale, Scirocco, Libeccio, Grecale
- EFFIS + VIIRS, event markers, CSV/GeoJSON loader
- Wind shifts, barbs coloured by Tmax, drill-down 72h

### WildWinds — v2 🌬
Multi-station wind analysis for fire-relevant wind systems.
- Dual fullscreen wind rose (independent mode per panel)
- Foehn sector overlay (SVG-based, axis-independent, multi-sector per station)
- Wind clock (Hour × Direction heatmap), Scatter (Wind vs VPD/RH)
- Persistence analysis, wind shift histogram
- Obs/FCT/Both toggle respected across all charts and tables
- Stations: Portugal (Norte/Levante), Sardegna (Maestrale/Scirocco), California (Santa Ana/Diablo/Sundowner)

### HDWc Batch — v29 📊
Multi-fire event analysis with HDWc engine.
- Batch ERA5 fetch for fire event databases (CSV/GeoJSON)
- Per-fire engine detection from loaded JSON packages
- **Monitoring tab**: multi-point daily HDWc, FWI, fire load index
  - Now supports loading multi-zone packages (array/object format)
  - Auto-assigns engine to points by bbox
- Sortable results table, PDF export, Leaflet map

### HDWc Builder — v8 ⚙️
Standalone engine configuration editor.
- Zone Manager tab: list, rename, clone, reorder, delete, export
- Leaflet map with bbox drag-handles (CartoDB Dark)
- Draw tool for bbox (click+drag)
- 7 geographic templates, multi-file import
- α calibration import from ERA5 export
- Validation: alpha curve max_jump ≤ 0.15, monotone thresholds, bbox

---

## Regional Config Packages
| Package | Zones | Calibration |
|---------|-------|-------------|
| `portugal_package_v2.json` | 11 | ERA5 Minho 2014–2024 |
| `mediterranean_megafire_package_v2.json` | 12 | EFFIS 927 events 2016–2025 |

---

## Changelog

### 2026-04-17 — HDWc v2: fourth night-score tier from `RH_wilt`
- **firemeteo v41 → v42**: new `vpd_night_extreme` threshold derived from regional `RH_wilt`; `calcNightScore_v2` fallback path now reaches `ns=3` when `vpd_night > vpd_night_extreme` **and** thermal `ns_temp ≥ 2` (strict Option B). `_applyEngineConfig` now uses `T_night_warm` as `T_ref` for VPD derivation instead of a fixed 20 °C. `_clearEngineConfig` reset extended to include `vpd_night_extreme`.
- **batch v28 → v29**: `_registerMonEngine` extended with `vpd_extreme` from `RH_wilt`, `T_ref = T_night_warm`; `ns()` extended with 4th tier (same strict Option B logic).
- **portugal v3 → v4, sardegna v5 → v6**: same four patches propagated.
- Mobile (v1) and WildWinds (v2) not affected — mobile has a simplified engine without nocturnal VPD modulator; WildWinds has no HDWc engine.
- Audit outcome: `ns`, `classifyDay`, `alpha`, `beta`, `_fclassBonus` are now verified identical between FireMeteo and Batch. Sole intentional divergence: night-score path (hourly window in FireMeteo vs daily `Tmin` in Batch, by design).

---

## Author
Giuseppe Cornaglia  
ANEPC — Autoridade Nacional de Emergência e Proteção Civil, Portugal  
Copernicus EMS National Focal Point — Portugal  
