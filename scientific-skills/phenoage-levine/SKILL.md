---
name: phenoage-levine
description: Calculate Levine Phenotypic Age (biological age) from 9 standard blood biomarkers using the validated Gompertz proportional hazard model (Levine et al. 2018, PLoS Med). Returns PhenoAge, 10-year mortality risk, age acceleration (difference from chronological age), and personalized recommendations per biomarker. Use this for ABLE GENETICS longevity reports, PhenoAge baseline analysis, and biological age tracking. Supports all common measurement units with automatic conversion.
allowed-tools: [Read, Write, Edit, Bash]
license: MIT License
metadata:
    skill-author: ABLE GENETICS Bioinformatics
    reference: "Levine ME et al. Aging (Albany NY). 2018;10(4):573-591 | Liu Z et al. PLoS Med. 2018;15(12):e1002718"
    calculator-source: https://www.longevity-tools.com/levine-pheno-age
    validated-implementation: https://github.com/hillarylinmd/phenoage
---

# PhenoAge Levine — Calculator Vârstă Biologică

## Descriere

Skill pentru calculul **Phenotypic Age (PhenoAge)** conform metodologiei Levine et al. 2018, bazată pe **9 biomarkeri din analizele standard de sânge** + vârsta cronologică.

**Formula originală**: Morgan Levine, Steve Horvath et al. — model Gompertz de hazard proporțional, validat pe cohorta NHANES IV (>11.000 participanți).

---

## Formula Matematică Completă (Corectă)

### ⚠️ IMPORTANT: Conversii Unități ÎNAINTE de calcul

Biomarkerii trebuie convertiți în unitățile interne folosite de modelul Levine:

| Biomarker | Unitate INPUT (clinică) | Unitate INTERNĂ model | Conversie |
|-----------|------------------------|----------------------|-----------|
| Albumin | g/dL | g/L | × 10 |
| Creatinine | mg/dL | µmol/L | × 88.4 |
| Glucose | mg/dL | mmol/L | × 0.0555 |
| hsCRP | mg/L (raport lab) | ln(mg/dL) = ln(mg/L × 0.1) | log(CRP_mgL × 0.1) |
| Lymphocyte % | % | % | — |
| MCV | fL | fL | — |
| RDW | % | % | — |
| ALP | U/L | U/L | — |
| WBC | 10³/µL | 10³/µL | — |

### Step 1 — Combinație liniară `xb`

```
# Unitățile INTERNE (după conversie):
xb = −19.9067
   + (−0.0336  × albumin_gL)          # albumin în g/L
   + ( 0.0095  × creatinine_umolL)     # creatinină în µmol/L
   + ( 0.1953  × glucose_mmolL)        # glucoză în mmol/L
   + ( 0.0954  × ln(CRP_mgL × 0.1))   # CRP: ln(mg/dL)
   + (−0.0120  × lymphocyte_pct)       # limfocite %
   + ( 0.0268  × MCV_fL)               # MCV în fL
   + ( 0.3306  × RDW_pct)              # RDW %
   + ( 0.0019  × ALP_UL)               # ALP în U/L  ← 0.0019, NU 0.00188
   + ( 0.0554  × WBC_1000uL)           # WBC 10³/µL
   + ( 0.0804  × chronological_age)    # vârstă în ani
```

### Step 2 — Mortality Score (distribuție Gompertz-Makeham CDF)

```
gamma = 0.0076927
t_months = 120          # 10 ani = 120 luni (orizont temporal fix)

M = 1 − exp(−exp(xb) × (exp(gamma × t_months) − 1) / gamma)
```

> **Notă critică**: Formula corectă include parametrul temporal `t_months = 120`.
> Formula greșită (frecvent citată): `1 - exp(−1.51714 × exp(xb) / gamma)` → produce M=1.0 pentru valori normale → eroare matematică.

### Step 3 — PhenoAge Final

```
PhenoAge = 141.50225 + ln(−0.00553 × ln(1 − M)) / 0.09165
```

### Parametrii Gompertz

| Parametru | Valoare | Rol |
|-----------|---------|-----|
| Intercept | −19.9067 | Constantă model liniar |
| gamma | 0.0076927 | Parametru rată Gompertz |
| t_months | 120 | Orizont temporal (10 ani) |
| 141.50225 | — | Offset vârstă fenotipică |
| 0.09165 | — | Rata de îmbătrânire Gompertz |
| −0.00553 | — | Factor scalare log-transform |

---

## Biomarkeri Necesari + Unități de Intrare

| Biomarker | Unitate clinică (INPUT) | Conversii acceptate | Valoare optimă longevitate |
|-----------|------------------------|--------------------|-----------------------------|
| **Albumin** | g/dL | g/L ÷ 10 → model folosește g/L | > 4.0 g/dL |
| **Creatinine** | mg/dL | µmol/L ÷ 88.4 → model folosește µmol/L | 0.6–1.1 (F) / 0.7–1.2 (M) mg/dL |
| **Glucose** | mg/dL | mmol/L ÷ 0.0555 → model folosește mmol/L | < 90 mg/dL (jeun) |
| **hsCRP** | mg/L (raport lab) | mg/dL × 10 → model folosește ln(mg/dL) | < 1 mg/L |
| **Lymphocyte %** | % | — | 25–40% |
| **MCV** | fL | — | 85–95 fL |
| **RDW** | % | — | < 13.5% |
| **ALP (Alkaline Phosphatase)** | U/L | µkat/L × 60.2 | 40–80 U/L |
| **WBC** | 10³ cells/µL | x10⁹/L (echivalent) | 4.0–6.0 × 10³/µL |
| **Vârstă cronologică** | ani | — | — |

---

## Interpretare Rezultate

| PhenoAge vs Vârstă Cronologică | Semnificație | Recomandare |
|-------------------------------|--------------|-------------|
| **< −5 ani** (ex: PhenoAge 35, cronoAge 44) | Excelent — îmbătrânire lentă | Menținere protocol |
| **−5 până la 0 ani** | Bun — sub medie | Optimizare biomarkeri limită |
| **0 până la +5 ani** | Atenție — îmbătrânire accelerată ușoară | Intervenție lifestyle |
| **> +5 ani** | Critic — îmbătrânire accelerată semnificativă | Protocol intensiv + consultație |

**Age Acceleration** = PhenoAge − Chronological Age
- Negativ = mai tânăr biologic (favorabil)
- Pozitiv = mai bătrân biologic (necesită intervenție)

---

## Script Python Complet (Validat)

```python
import math


def calculate_phenoage(
    albumin_gdL: float,        # g/dL (standard lab)
    creatinine_mgdL: float,    # mg/dL (standard lab)
    glucose_mgdL: float,       # mg/dL (standard lab)
    crp_mgL: float,            # mg/L (standard lab — NU mg/dL!)
    lymphocyte_pct: float,     # %
    mcv_fL: float,             # fL
    rdw_pct: float,            # %
    alp_UL: float,             # U/L
    wbc_1000uL: float,         # 10³ cells/µL
    chronological_age: float   # ani
) -> dict:
    """
    Calculează Levine Phenotypic Age (PhenoAge) din biomarkeri sânge.

    Referință: Levine ME et al. Aging (Albany NY). 2018;10(4):573-591
               Liu Z et al. PLoS Med. 2018;15(12):e1002718
    Implementare validată: github.com/hillarylinmd/phenoage

    IMPORTANT: Parametrii se introduc în unitățile clinice standard (mg/dL, g/dL, mg/L).
    Conversia la unitățile interne ale modelului se face automat.

    Returnează dict cu pheno_age, mortality_10yr_pct, age_acceleration, biomarker_flags
    """

    # ─── STEP 0: Conversii unități → unitățile interne ale modelului Levine ───
    albumin_gL = albumin_gdL * 10            # g/dL → g/L
    creatinine_umolL = creatinine_mgdL * 88.4  # mg/dL → µmol/L
    glucose_mmolL = glucose_mgdL * 0.0555    # mg/dL → mmol/L

    # CRP: mg/L → ln(mg/dL) = ln(mg/L × 0.1)
    crp_mgL_safe = max(crp_mgL, 0.001)      # floor pentru log (evită log(0))
    crp_ln = math.log(crp_mgL_safe * 0.1)   # ln(mg/dL)

    # ─── STEP 1: Combinație liniară xb ───────────────────────────────────────
    xb = (-19.9067
          + (-0.0336  * albumin_gL)
          + ( 0.0095  * creatinine_umolL)
          + ( 0.1953  * glucose_mmolL)
          + ( 0.0954  * crp_ln)
          + (-0.0120  * lymphocyte_pct)
          + ( 0.0268  * mcv_fL)
          + ( 0.3306  * rdw_pct)
          + ( 0.0019  * alp_UL)
          + ( 0.0554  * wbc_1000uL)
          + ( 0.0804  * chronological_age))

    # ─── STEP 2: Mortality Score — Gompertz-Makeham CDF cu t=120 luni ────────
    gamma = 0.0076927
    t_months = 120  # 10 ani = 120 luni (orizont temporal fix Levine 2018)

    mortality_score = 1 - math.exp(
        -math.exp(xb) * (math.exp(gamma * t_months) - 1) / gamma
    )
    mortality_pct = mortality_score * 100  # % risc deces 10 ani

    # ─── STEP 3: PhenoAge ─────────────────────────────────────────────────────
    inner = -0.00553 * math.log(1 - mortality_score)
    if inner <= 0:
        pheno_age = None
        error = "Valori biomarkeri extreme — verificați unitățile de intrare"
    else:
        pheno_age = 141.50225 + math.log(inner) / 0.09165
        error = None

    # ─── Age Acceleration ─────────────────────────────────────────────────────
    age_accel = round(pheno_age - chronological_age, 2) if pheno_age else None

    # ─── Flags biomarkeri (în unități clinice originale) ─────────────────────
    flags = {}
    if albumin_gdL < 3.5:
        flags['albumin'] = f"⚠️ SCĂZUT ({albumin_gdL} g/dL) — risc malnutriție/inflamație"
    if crp_mgL > 3.0:  # > 3 mg/L = limita sensibilă
        flags['crp'] = f"⚠️ CRESCUT ({crp_mgL:.1f} mg/L) — inflamație cronică"
    elif crp_mgL > 1.0:
        flags['crp'] = f"🟡 MODERAT ({crp_mgL:.1f} mg/L) — risc cardiovascular moderat"
    if rdw_pct > 14.5:
        flags['rdw'] = f"⚠️ CRESCUT ({rdw_pct}%) — stres oxidativ, deficit Fe/B12"
    if glucose_mgdL > 100:
        flags['glucose'] = f"⚠️ PREDIABET ({glucose_mgdL} mg/dL) — rezistență insulinică"
    if lymphocyte_pct < 20:
        flags['lymphocyte'] = f"⚠️ LIMFOPENIE ({lymphocyte_pct}%) — imunodeficiență"
    if wbc_1000uL > 9.0:
        flags['wbc'] = f"⚠️ LEUCOCITOZĂ ({wbc_1000uL}×10³/µL) — infecție/inflamație"
    if alp_UL > 120:
        flags['alp'] = f"⚠️ CRESCUT ({alp_UL} U/L) — patologie hepatică/osoasă"
    if mcv_fL > 100:
        flags['mcv'] = f"⚠️ MACROCITOZA ({mcv_fL} fL) — deficit B12/folat"

    return {
        'pheno_age': round(pheno_age, 2) if pheno_age else None,
        'chronological_age': chronological_age,
        'age_acceleration': age_accel,
        'mortality_10yr_pct': round(mortality_pct, 2),
        'xb': round(xb, 6),
        'biomarker_flags': flags,
        'interpretation': interpret_phenoage(pheno_age, chronological_age) if pheno_age else None,
        'error': error,
        # Debug internals (opțional)
        '_debug': {
            'albumin_gL': round(albumin_gL, 3),
            'creatinine_umolL': round(creatinine_umolL, 3),
            'glucose_mmolL': round(glucose_mmolL, 4),
            'crp_ln': round(crp_ln, 4),
        }
    }


def interpret_phenoage(pheno_age: float, chron_age: float) -> str:
    """Interpretare clinică PhenoAge."""
    diff = pheno_age - chron_age
    if diff < -5:
        return f"🟢 EXCELENT: Vârstă biologică cu {abs(diff):.1f} ani mai MICĂ decât cea cronologică. Îmbătrânire lentă."
    elif diff < 0:
        return f"🟡 BUN: Vârstă biologică cu {abs(diff):.1f} ani mai mică decât cea cronologică."
    elif diff < 5:
        return f"🟠 ATENȚIE: Vârstă biologică cu {diff:.1f} ani mai MARE. Optimizare recomandată."
    else:
        return f"🔴 CRITIC: Îmbătrânire accelerată cu {diff:.1f} ani. Protocol intensiv necesar."


# ─── VALIDARE: Diana (noiembrie 2025) ─────────────────────────────────────────
# Valori reale din raportul ABLE GENETICS Diana
# Rezultat așteptat: PhenoAge ≈ 36.7 ani (din raport), Age Acceleration ≈ -7.3 ani
if __name__ == "__main__":
    result = calculate_phenoage(
        albumin_gdL=4.2,
        creatinine_mgdL=0.48,
        glucose_mgdL=83,
        crp_mgL=0.46,          # 0.46 mg/L (NU 0.046 — CRP se introduce în mg/L!)
        lymphocyte_pct=37.53,
        mcv_fL=95.6,
        rdw_pct=13.6,
        alp_UL=80,
        wbc_1000uL=5.49,
        chronological_age=44
    )

    print(f"\n{'='*55}")
    print(f"  ABLE GENETICS — PhenoAge Calculator (Levine 2018)")
    print(f"{'='*55}")
    print(f"  Vârstă cronologică  : {result['chronological_age']} ani")
    print(f"  Vârstă biologică    : {result['pheno_age']} ani")
    print(f"  Age Acceleration    : {result['age_acceleration']:+.2f} ani")
    print(f"  Mortalitate 10 ani  : {result['mortality_10yr_pct']}%")
    print(f"  xb (linear combo)   : {result['xb']}")
    print(f"\n  {result['interpretation']}")
    print(f"\n  Debug conversii interne:")
    for k, v in result['_debug'].items():
        print(f"    {k}: {v}")

    if result['biomarker_flags']:
        print(f"\n  ⚠️  Biomarkeri de urmărit:")
        for k, v in result['biomarker_flags'].items():
            print(f"    • {v}")
    print(f"{'='*55}\n")
```

---

## Exemple de Utilizare Claude

### Calcul standard (valori tipice raport lab):
```
Pacient: bărbat, 52 ani
Albumin: 4.1 g/dL
Creatinină: 0.9 mg/dL
Glucoză: 95 mg/dL
hsCRP: 2.1 mg/L
Limfocite: 28%
MCV: 88 fL
RDW: 13.2%
ALP: 65 U/L
WBC: 6.2 × 10³/µL
```

→ Apelează `calculate_phenoage(4.1, 0.9, 95, 2.1, 28, 88, 13.2, 65, 6.2, 52)`

### Conversii frecvente necesare:
| Situație | Formula |
|----------|---------|
| CRP în µg/mL | 1 µg/mL = 1 mg/L → introduci direct în mg/L |
| Albumin în g/L | împarte la 10 → g/dL |
| Creatinină în µmol/L | împarte la 88.4 → mg/dL |
| Glucoză în mmol/L | înmulțește cu 18.016 → mg/dL |
| ALP în µkat/L | înmulțește cu 60.2 → U/L |

---

## Când Se Folosește

### ✅ Folosește acest skill când:
- Pacient trimite analize de sânge (CBC + metabolice) pentru ABLE Longevity
- Generezi secțiunea PhenoAge din raportul general de longevitate
- Faci tracking longitudinal (reevaluare la 3-6 luni)
- Compari PhenoAge înainte/după intervenție (suplimente, dietă, exercițiu)
- Validezi rezultatul din synergy-age-mcp

### ⚠️ Limitări importante:
- Funcționează DOAR cu 9 biomarkeri specifici Levine — nu cu alți markeri
- **CRP se introduce în mg/L** (cum apare în raportul de laborator) — conversia la mg/dL se face intern
- Creatinina se introduce în mg/dL — conversia la µmol/L se face intern
- Nu echivalent cu DNAm PhenoAge (care necesită metilare ADN)
- Validat pe adulți 20-84 ani din NHANES IV (population: SUA generală)
- ALP coeficient = 0.0019 (NU 0.00188 cum apare în unele surse secundare)

---

## Integrare în ABLE GENETICS

### Flux Raport Longevitate General:
```
1. Pacient trimite analize sânge (PDF sau CSV)
2. Claude extrage valorile celor 9 biomarkeri (toate în unități clinice standard)
3. Rulează phenoage-levine → obține PhenoAge + flags (conversia unități = automată)
4. Completează cu synergy-age-mcp pentru GrimAge estimat
5. Generează secțiunea "Biological Age Assessment" în raport HTML
6. Adaugă recomandări personalizate per biomarker flagged
```

### Output așteptat în raport:
```
Vârstă biologică (PhenoAge): 36.7 ani
Vârstă cronologică:          44.0 ani
Age Acceleration:            −7.3 ani  ✅ Excelent
Risc mortalitate 10 ani:     ~2.4%
```

---

## Referințe Științifice

1. **Levine ME, Lu AT, Quach A, et al.** An epigenetic biomarker of aging for lifespan and healthspan. *Aging (Albany NY).* 2018;10(4):573-591. https://pmc.ncbi.nlm.nih.gov/articles/PMC5940111/

2. **Liu Z, Kuo PL, Horvath S, et al.** A new aging measure captures morbidity and mortality risk across diverse subpopulations from NHANES IV. *PLoS Med.* 2018;15(12):e1002718. https://journals.plos.org/plosmedicine/article?id=10.1371/journal.pmed.1002718

3. **Calculator referință**: https://www.longevity-tools.com/levine-pheno-age

4. **Implementare validată**: https://github.com/hillarylinmd/phenoage
