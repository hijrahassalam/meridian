# Meridian Config Experiments

Tujuan: menemukan config optimal berdasarkan data, bukan tebakan.

## Cara Pakai

1. Setiap config = 1 branch (`config/vX-nama`)
2. Jalankan minimal 48 jam sebelum evaluasi
3. Catat hasil di tabel bawah
4. Best config → promoted ke `config/production`

## Eval Metrics

| Metric | Target | Cara Hitung |
|---|---|---|
| Candidates/day | 3-10 | Dari screening log |
| Positions opened/day | 1-3 | Dari state.json |
| Win rate | >60% | TP / (TP + SL) |
| Avg PnL | >0% | Rata-rata PnL per position |
| Avg hold time | 2-12h | Waktu deploy → close |
| Fee earned | >IL | Total fee vs impermanent loss |

## Experiment Log

### v1-baseline (DEFAULT)
- Branch: `config/v1-baseline`
- Start: 2026-05-14
- Status: TESTING
- Changes from upstream:
  - screeningSource: gmgn (upstream default: meteora)
  - blockedLaunchpads: [pump.fun, letsbonk.fun] (upstream: [])
  - chartIndicators.enabled: true (upstream: false)
  - athFilterPct: -20 (upstream: null)
  - autoSwapAfterClaim: true (upstream: false)
- Key params:
  - minTvl: 10000, minVolume: 500, minOrganic: 60
  - minTokenFeesSol: 30, minMcap: 150000, maxMcap: 10000000
  - maxBotHoldersPct: 30, maxBundlePct: 30, maxTop10Pct: 60
  - stopLossPct: -50, takeProfitPct: 5
  - GMGN: minTotalFeeSol: 30, maxBotDegenRate: 0.4, maxBundlerRate: 0.5
- Results:
  - Candidates/day: —
  - Positions opened: —
  - Win rate: —
  - Avg PnL: —
  - Notes: —

### v2-tuned (CURRENT LIVE)
- Branch: `config/v2-tuned`
- Start: 2026-05-13
- Status: STARVED (0 candidates)
- Changes from v1:
  - minTvl: 10000→15000, minVolume: 500→2000, minOrganic: 60→75
  - minTokenFeesSol: 30→50, minMcap: 150k→200k, maxMcap: 10M→3M
  - maxBotHoldersPct: 30→25, maxBundlePct: 30→20, maxTop10Pct: 60→50
  - stopLossPct: -50→-15, timeframe: 5m→4h
  - GMGN: minTotalFeeSol: 30→50, maxBotDegenRate: 0.4→0.25, maxBundlerRate: 0.5→0.2
  - maxTokenAgeHours: 168, minTokenAgeHours: 24
- Results:
  - Candidates/day: 0 (too strict)
  - Funnel: 100→7→2→0→0→0
  - Notes: Double-filter too aggressive, starved pipeline

## Side-by-Side Comparison

| Parameter | v1-baseline | v2-tuned | v3-xxx |
|---|---|---|---|
| **Screening** | | | |
| minTvl | 10,000 | 15,000 | — |
| minVolume | 500 | 2,000 | — |
| minOrganic | 60 | 75 | — |
| minMcap | 150,000 | 200,000 | — |
| maxMcap | 10,000,000 | 3,000,000 | — |
| minTokenFeesSol | 30 | 50 | — |
| maxBundlePct | 30 | 20 | — |
| maxBotHoldersPct | 30 | 25 | — |
| maxTop10Pct | 60 | 50 | — |
| **GMGN** | | | |
| minTotalFeeSol | 30 | 50 | — |
| maxBotDegenRate | 40% | 25% | — |
| maxBundlerRate | 50% | 20% | — |
| minHolders | 1,000 | 500 | — |
| minTokenAgeHours | 2 | 24 | — |
| **Risk** | | | |
| stopLossPct | -50% | -15% | — |
| timeframe | 5m | 4h | — |
| outOfRangeBinsToClose | 10 | 8 | — |
| outOfRangeWaitMinutes | 30 | 10 | — |
| **Results** | | | |
| Candidates/day | — | 0 | — |
| Positions/day | — | 0 | — |
| Win rate | — | N/A | — |
| Avg PnL | — | N/A | — |
| Status | TESTING | STARVED | — |

## Planned Experiments

- [x] v1-baseline: Default values + GMGN + safety additions
- [ ] v3-moderate: Between v1 and v2 (e.g. minTvl=12000, minTokenFeesSol=35)
- [ ] v4-aggressive: Even looser than v1 (minTvl=8000, minTokenFeesSol=20)
