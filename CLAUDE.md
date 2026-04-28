# CLAUDE.md — Reguli Obligatorii pentru ABLE GENETICS

## ⚠️ REGULI BUSINESS CRITICE

### 1. Secvențiere WES/WGS — Partener EXCLUSIV: Dante Genomics

ABLE GENETICS are colaborare B2B cu **Dante Genomics** pentru toate serviciile de secvențiere avansată (WES + WGS).

**INTERZIS**: Nu recomanda niciodată alți provideri de secvențiere (Blueprint Genetics, Invitae, GeneDx, Centogene, etc.) în contextul ABLE GENETICS.  
**CORECT**: Toate recomandările de WES/WGS → direcționează exclusiv către **Dante Genomics** prin ABLE GENETICS.

---

### 2. Panel NGS EDS Clinic — Specificații Tehnice Obligatorii

Când recomandăm confirmare diagnostică EDS prin panel NGS clinic, panelul trebuie să includă **obligatoriu**:

| Genă | Motiv |
|------|-------|
| `TNXB` + **MLPA** | MLPA obligatoriu — deleții mari frecvente (~5-10%), nedetectabile prin NGS standard |
| `COL5A1` / `COL5A2` | EDS Classic |
| `COL1A1` / `COL1A2` | EDS Clasic + Artrocalazic |
| `COL3A1` **complet** | EDS Vascular — toate exonii, critică pentru diagnostic |
| `COL12A1` | EDS Myopathic spectrum |
| `PLOD1` / `PLOD2` | EDS Kyphoscoliotic |
| `FBN1` / `FBN2` | Marfan / Contractural Arachnodactyly |

---

### 3. Rolul ABLE GENETICS și Valoare Medico-Legală

**Datele genomice sunt secvențiate de Dante Genomics (laborator acreditat). ABLE GENETICS realizează interpretarea bioinformatică** — adnotare variante, scoring, analiză pathway-uri, generare raport.

**Raportul ABLE nu are valoare medico-legală** — pentru diagnostic formal, litigii sau asigurări, este necesar un raport semnat de genetician clinic.

**Model corect de comunicare:**

> *"Secvențierea este realizată de Dante Genomics (laborator acreditat). ABLE GENETICS oferă interpretarea bioinformatică. Pentru uz medico-legal, este necesar un raport semnat de genetician."*

**Când este necesar un raport semnat de genetician (pe lângă interpretarea ABLE):**
- Cancer ereditar (BRCA1/2, Lynch, Li-Fraumeni) → raport genetician obligatoriu
- Diagnostic EDS formal cu implicații medico-legale → + MLPA obligatoriu
- Boli Mendeliene rare cu decizie terapeutică majoră
- Testare prenatală / preimplantare
- Utilizare în litigii juridice, asigurări, expertize medicale

**Modelul de lucru:**
`Dante Genomics (secvențiere acreditată) → ABLE GENETICS (interpretare bioinformatică) → Genetician clinic (raport medico-legal, dacă e necesar)`

---

### 4. Rapoarte Genomice

- Rapoartele ABLE GENETICS se generează în **HTML** cu export **DOCX** și **PDF**
- Versiunile se incrementează (v1, v2, ... vN)
- Documentația completă: `/Users/andreibotescu/Downloads/ABLE_GENETICS_COMPREHENSIVE_DOCUMENTATION.md`

---

### 5. QA Gate Obligatoriu la Finalizare Raport (din review medical martie 2026)

**AF Gate — Allele Frequency:**
- AF > 50% → polimorfism populațional majoritar, **NU vulnerabilitate** (ex: SIRT6 rs352493 AF 88.5%)
- AF > 30% → informativ doar, fără bază terapeutică (ex: IFIH1 rs3747517 AF 67.8%)
- AF 5-30% → nuanțat, OR modest, menționează că e polimorfism comun

**Variant-Specific Check:** Verifică ÎNTOTDEAUNA dacă varianta este cea **clasică patogenă** pentru boala asociată (ex: NOD2 R702W/G908R/3020insC pt Crohn, nu orice variantă NOD2)

**Ton:** Zero cuvinte alarmiste (critic, urgent, grav, sever). Genotip = predispoziție, NU diagnostic.

**Doze:** Fiecare doză verificată cu studii clinice. TMG ≥1.5g/zi (nu 1g). Bismut max 2 săpt. Fisetin 100mg = antioxidant (senolitic = 20mg/kg). Minerale: specifică elemental vs sare.

**Lab înainte de protocol:** Homocisteină, B12, folat, feritină ÎNAINTE de dozare MTHFR. Dozele se calibrează pe biologia reală, nu doar genotip.

**Substanțe de cercetare:** Peptide (BPC-157, MOTS-c) = disclaimer explicit: non-aprobate EMA/FDA, studii predominant preclinice.

---

### 6. Premium F v2 Spec (OBLIGATORIU pentru rapoarte Premium F)

**Source of truth:** `/Users/andreibotescu/Desktop/ABLE GENETICS/pipeline/specs/premium_f_v2.md`

Orice pachet Premium F (1 raport principal + 7 tematice) **trebuie** să respecte spec-ul. Fiecare skill / orchestrator / agent trebuie să citească spec-ul înainte de execuție.

**Standarde post-Anca v5 obligatorii:**
- **EVEE** (Goodfire+Mayo) pe variante actionable
- **R analytics** (17 scripturi `pipeline/r_scripts/`)
- **AF Gate v2** (score cap 2 pt AF>50%, 3 pt AF 30-50%)
- **Mito Health Index** (ABLE intern) — NU "GrimAge proxy" (halucinație, scos din cod)
- **MTHFR variant-specific** — C677T (rs1801133, formă majoră) vs A1298C (rs1801131, formă moderată) tratate distinct
- **ApoB + Lp(a)** obligatoriu per ESC/EAS 2025 în secțiunea Cardio
- **Limbaj măsurat** — hook QA aplicat automat; APOE ε4 "genotipul nu determină destinul"
- **PMID pe fiecare recomandare** + `verify_recommendations.py`
- **Per-axis subagent depth** — 10 subagenți paraleli pt main, 7 pt tematice (Anca v5 baseline: 17K cuvinte main, 3.3K/tematic)
- **`verify_evee_output()`** enforcement la final pipeline
- **`verify_premium_f_spec.py`** + **`cross_check_premium_f.py`** obligatorii după pipeline

**CLI flag:** `python3 run_report.py --patient X --package premium_F --premium-f` rulează automat validatorii.
**Strict mode:** `STRICT_PREMIUM_F=1` → fail-fast dacă spec neîndeplinit.



---

### 7. No Resource Rediscovery (Foundational Rule)

**SessionStart hook** la `~/.claude/hooks/able_resource_inventory.sh` injectează automat la începutul fiecărei sesiuni:
- R path (`/opt/homebrew/bin/Rscript` 4.5.3)
- Python packages (numpy, matplotlib, plotly, docx)
- Pipeline scripts cu paths absoluți
- Spec versions
- Latest patient

**REGULĂ:** NU rula `which R`, `pip install`, `find`, etc. dacă cunoaștem paths din SessionStart inventory. Folosește direct paths cunoscute.

**Excepție:** Doar dacă inventory NU listează resursa căutată.

---

### 8. Premium F v3 standards (NEW post-Anca v5)

Spec actualizat: `pipeline/specs/premium_f_v3.md` (bump de la v2)

**Standarde adăugate v3:**
- **ACMG formal classification** per missense actionable (skill `able-genetics-acmg-classification`)
- **UniProt protein annotation** per missense (skill `able-genetics-uniprot-annotation`)
- **AlphaMissense scoring** per missense (skill `able-genetics-alphamissense-scoring`)
- **Plain-language Introducere** section (350-450 cuv main, 100-200 tematic) — Anca v5 pattern
- **Glosar 30+ termeni** la final (skill `able-genetics-report-glossary`)
- **Verification links** (ClinVar/dbSNP/UniProt URLs) în tabele variante

**Pentru disseminare:**
- Anonimizare via `able-genetics-showcase-anonymizer`
- Executive one-pager via `able-genetics-executive-dashboard`
- NotebookLM infographic
- Folder `showcase_model/` separat

---

### 9. Stil narativ-pedagogic obligatoriu pentru rapoarte

**Standard:** `wiki/standards/report_narrative_style.md`
**Memory:** `feedback_report_narrative_style.md`
**Hook activ:** `~/.claude/hooks/able_narrative_style_check.sh` (PreToolUse pe Write|Edit)

**Reguli stricte:**
1. ZERO `**bold**` markdown în corp text. Bold doar pentru titluri tabele și valori KPI critice.
2. ZERO em-dashes (—) în corp text. Înlocuiește cu virgulă, paranteză, punct, două puncte. Threshold hook: max 10 per fișier.
3. NUME pacient doar pe cover. În corp: "tu/tine/profilul tău/genele tale". NU "Pentru [Nume]" sau "[Nume], 48F". Hook blochează la 1+ ocurență.
4. Bullet permis doar pentru liste enumerative reale (KPI, suplimente cu doză, biomarkeri). Paragrafe narative pentru explicații mecanism și interpretare variantă.

**Structură pedagogică obligatorie per axă/panel — pattern de 4 părți:**
1. Ce este această axă în contextul longevității (paragraf narativ)
2. Cum funcționează biologic (cale moleculară, paths cheie)
3. Variantele tale și ce înseamnă molecular (per variant: gena → proteină → ce schimbă mutația → efect clinic → PMID + cross-validation ACMG/AlphaMissense/EVEE/ClinVar)
4. Cum poți interveni concret (lifestyle → suplimente după lab → opțiuni medicale)

**Pentru main report — secțiune extra obligatorie "Bun venit în lumea longevității":**
- Vârstă cronologică vs biologică (PhenoAge ca exemplu)
- 5 hallmarks of aging (DNA damage, mitochondrial dysfunction, inflammaging, senescent cells, telomere attrition)
- Cum se traduce profilul genetic în factori modificabili
- Cum citești raportul

**Humanizer skill obligatoriu** după rescriere — elimină construcții formulaice, repetări de structură, vocabular marketing, tranziții AI.
