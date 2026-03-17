# ABLE GENETICS — NotebookLM Diagram Prompts Library

Standard prompts for generating explanatory diagrams from ABLE GENETICS genomic reports.
All prompts are designed for use with `notebooklm generate` commands.

## Category 1: Gene Interaction Diagrams

### Full Gene Network Mind Map
```
Create a comprehensive gene interaction mind map for this genomic report.
Central node: Patient name and PhenoAge.
Organize into 6 clusters:
1. MITOCHONDRIAL AXIS: SOD2 (Val/Val vulnerability) → NQO1 (het) → CoQ10/Ubiquinone pathway → SIRT3 (deacetylation) → NAD+ consumption
2. INFLAMMATORY AXIS: NLRP3 (GoF) → IL-1β/IL-18 → NF-κB → TNF-α (rs1800629) → IL10 (insufficient brake, AG)
3. CARDIOVASCULAR AXIS: APOE E4 → LDL/VLDL clearance → cholesterol crystal → NLRP3 activation; SLCO1B1 (safe statins); PON1 (HDL protection)
4. METABOLIC AXIS: TCF7L2 AA → insulin signaling → PPARG Pro/Ala (protective); FTO (appetite); AMPK pathway
5. LONGEVITY AXIS: FOXO3 GG → SOD2/CAT/GADD45; SIRT3; TERT (telomere); PPARG
6. METHYLATION AXIS: MTHFR A1298C → 5-MTHF → MTRR/MTR → B12 → Homocysteine → SAM
Use green for protective variants, red for risk variants, yellow for heterozygous.
Show supplement interventions in blue boxes connected to their target genes.
```

### FOXO3 Activation Network
```
Create a detailed network diagram showing FOXO3 regulation:

ACTIVATION pathways (green arrows):
- Exercise → AMPK → SIRT1 → FOXO3 deacetylation → nuclear entry
- Fasting/IF → AMPK + reduced insulin → FOXO3 activation
- NMN → NAD+ → SIRT1/SIRT3 → FOXO3
- Resveratrol → SIRT1 → FOXO3

SUPPRESSION pathways (red arrows):
- Insulin/IGF-1 → PI3K → AKT → FOXO3-Ser256 phosphorylation → cytoplasmic retention
- Chronic inflammation → IL-1β (NLRP3 GoF) → NF-κB → FOXO3 suppression
- High glucose → insulin spike → suppression (TCF7L2 risk)

FOXO3 TARGETS (downstream, purple):
- SOD2 transcription ↑ (compensates Val/Val deficit)
- Catalase ↑
- GADD45 → DNA repair
- PINK1 → mitophagy initiation
- p27/p21 → cell cycle arrest (anti-cancer)
- Apoptosis genes (BAX, FasL) → senescent cell clearance

Mark: "Mihai's FOXO3 GG = higher basal expression. But NLRP3 GoF suppresses it. Protocol goal: remove the suppression."
```

### SOD2 Mitochondrial Import Mechanism
```
Create a mechanistic diagram showing:

NORMAL (Ala/Ala genotip):
Ala16 targeting sequence → efficient mitochondrial import → SOD2 in matrix → O2•− → H2O2 → catalase → H2O (complete detoxification)

MIHAI (Val/Val genotip, rs4880 TT):
Val16 targeting sequence → REDUCED mitochondrial import efficiency (30-40% less) → insufficient SOD2 in matrix → O2•− ACCUMULATES → oxidizes mtDNA → damaged mitochondria → NLRP3 activation → IL-1β

SIRT3 ACTIVATION (compensatory):
NAD+ (from NMN) → SIRT3 deacetylase → deacetylates SOD2 at Lys68/Lys122 → ACTIVATES remaining SOD2 → improved activity

MITOQ MECHANISM:
MitoQ = CoQ10 + TPP+ (triphenylphosphonium) → accumulates 1000× in mitochondrial matrix (driven by membrane potential −180mV) → accepts electrons directly from Complex I/II → quenches O2•− in situ

Data: OpenGenes DB — SOD2 overexpression in Drosophila: +34.5% mean lifespan; in C. elegans: +54-120% lifespan.
Reference: Chen Y et al. (2011) J Biol Chem. SIRT3 activates SOD2 by deacetylation.
```

## Category 2: PhenoAge & Biomarker Diagrams

### PhenoAge Calculator Visual
```
Create a visual calculator infographic for PhenoAge (Levine 2018):

Title: "Calculul PhenoAge — Mihai Berbece"

Show 9 biomarker inputs as gauges/meters:
1. Albumina: 4.3 g/dL [green zone] — contribution: −1.34 ani
2. Creatinina: 0.87 mg/dL [green] — contribution: −0.42 ani
3. Glucoză: 87.5 mg/dL [green, near yellow] — contribution: −1.52 ani
4. hs-CRP: 0.42 mg/L [green] — contribution: −0.89 ani
5. Limfocite%: 34.2% [green] — contribution: −0.28 ani
6. MCV: 88.1 fL [green] — contribution: −0.47 ani
7. RDW: 13.8% [YELLOW WARNING] — contribution: −4.68 ani (DRIVER!)
8. Fosfataza alcalina: 52 IU/L [green] — contribution: −0.21 ani
9. WBC: 5.8 ×10⁹/L [green] — contribution: +0.27 ani

Formula box: "Lin combo → Gompertz-Makeham mortality function → PhenoAge = 39.2 ani"

Bottom: "Potențial cu protocol ABLE: −7.54 ani → PhenoAge țintă: 31.7 ani la 12 luni"

Highlight in red/orange: RDW as the single biggest optimization target.
Reference: Levine ME et al. (2018) Aging (Albany NY).
```

### Optimization Trajectory
```
Create a before/after roadmap showing expected biomarker changes over 12 months:

Timeline: Baseline (Feb 2026) → Month 3 → Month 6 → Month 12

Key biomarkers to show trajectory:
- RDW: 13.8% → 13.2% → 12.8% → 12.5% (MetilB12 + MetilFolat)
- hs-CRP: 0.42 → 0.3 → 0.2 → <0.2 (Omega-3 + NMN + NLRP3 control)
- Glucoză: 87.5 → 85 → 83 → <82 (IF 16:8 + Berberină)
- Homocisteina (estimated): likely 12-14 → target <9 (methylation stack)
- VitD 25-OH (estimated): likely 25-30 → target 60-80 (5000 IU D3)
- PhenoAge: 39.2 → ~37 → ~35 → ~32 (integrated improvements)

Show which protocol drives each improvement.
Add "Investigații recomandate" checkpoints at Month 0, 3, 6, 12.
```

## Category 3: Pathway Mechanism Diagrams

### NLRP3 Inflammasome — Complete Cascade
```
Draw the NLRP3 inflammasome cascade for this patient:

SIGNAL 1 (Priming, NF-κB dependent):
TNF-α / LPS / bacterial PAMPs → TLR4/TNFR → NF-κB → NLRP3 gene expression ↑, Pro-IL-1β expression ↑
(TNF rs1800629 GA heterozygote = amplified TNF signal)

SIGNAL 2 (Activation triggers — ALL elevated in Mihai):
• SOD2 Val/Val → mitochondrial O2•− → mtROS → NLRP3 activation
• APOE E4 → cholesterol crystals → lysosomal rupture → NLRP3
• TCF7L2 AA → glucose spikes → metabolic stress → NLRP3
• MTHFR → elevated homocysteine → endothelial ROS → NLRP3

ASSEMBLY (Gain-of-Function — lower threshold):
NLRP3 + ASC adaptor + Caspase-1 → inflammasome complex

OUTPUT:
Caspase-1 activation →
① Pro-IL-1β → IL-1β (mature, secreted) → systemic inflammation
② Pro-IL-18 → IL-18 → IFN-γ induction
③ Gasdermin-D cleavage → pores → pyroptosis (senescent cell death)
(NOTE: Mihai = chronic low-grade, NOT pyroptosis — sustained IL-1β leakage)

CONSEQUENCES:
IL-1β → NF-κB → more TNF-α (feed-forward loop)
IL-1β → AKT/IRS pathway → insulin resistance (TCF7L2 worsening)
IL-1β → FOXO3 suppression via JNK pathway

INTERVENTIONS (draw as red X blocks):
• Omega-3 EPA: blocks AA eicosanoid pathway (upstream of signal 2)
• Quercetin: direct NLRP3 protein binding → assembly inhibition
• NMN: reduces mitochondrial ROS substrate
• IF 16:8: reduces metabolic NLRP3 triggers
• MitoQ: quenches mtROS in matrix (most upstream fix)

Clinical reference: OpenGenes DB: NLRP3 knockout +27.9% max lifespan in mice.
```

### Vitamin D Complete Pathway
```
Create step-by-step vitamin D metabolism pathway for this patient.

Step 1 — INPUT:
Sun (UVB 290-315nm) → 7-dehydrocholesterol in skin → Pre-Vitamin D3 → thermal isomerization → Vitamin D3 (cholecalciferol)
OR Diet: fatty fish, egg yolk, supplements

Step 2 — LIVER (CYP2R1/CYP27A1):
D3 → 25-hydroxyvitamin D3 [25(OH)D3]
Patient variant: CYP2R1 rs10741657 — reduced 25-hydroxylase efficiency
→ NEEDS higher D3 input for same output

Step 3 — TRANSPORT (GC protein):
25(OH)D3 binds Vitamin D Binding Protein (GC/VDBP)
Patient variants: GC rs4588 + rs7041 → reduced binding affinity → reduced transport efficiency
→ More 25(OH)D3 lost, less reaches kidney

Step 4 — KIDNEY (CYP27B1):
25(OH)D3 → 1,25(OH)2D3 [Calcitriol — ACTIVE FORM]
(Regulated by PTH, calcium, phosphate)

Step 5 — VDR RECEPTOR:
Calcitriol → binds VDR
Patient: VDR FokI CT (rs2228570, CADD 26.9) → f allele → shorter VDR protein → REDUCED transcription efficiency
Also: VDR BsmI rs1544410 "Likely risk allele"

Step 6 — GENOMIC ACTION:
VDR-RXR heterodimer → VDREs → activates 2776 genomic targets including:
- CATHELICIDIN (antimicrobial)
- CYP24A1 (D metabolism)
- RANKL/OPG (bone)
- FOXO3 activation
- NLRP3 suppression

WHY 5000 IU:
Normal dose 2000 IU: CYP2R1 partial + GC transport reduced + VDR CT = final calcitriol output ~40% of expected
5000 IU: compensates for all three bottlenecks → achieves target 25-OH-VitD 60-80 ng/mL

Monthly cost: ~5 RON. Monitoring: 25-OH-VitD blood test at month 3.
```

### Methylation Cycle — One Carbon Pool
```
Draw the complete methylation cycle with patient-specific bottlenecks:

FOLATE ENTRY:
Diet/Supplement → Folic acid (SYNTHETIC — BLOCKED in this patient) → DHFR → THF
OR: Metilfolat 5-MTHF (direct bypass of MTHFR) ← CORRECT SUPPLEMENTATION

CYCLE:
THF → Serine Hydroxymethyltransferase (SHMT) → 5,10-methylene-THF
↓
MTHFR enzyme: 5,10-MTHF → 5-MTHF
[PATIENT: A1298C CT variant → REDUCED activity ~35%] ← RED BOTTLENECK #1
↓
5-MTHF → MTR (Methionine Synthase, B12-dependent) → Methionine
[PATIENT: MTR rs1805087 AG → reduced activity] ← BOTTLENECK #2
[PATIENT: MTRR rs1801394 AG → impaired B12 recycling to MTR] ← BOTTLENECK #3
[PATIENT: FUT2 rs602662 → impaired B12 intestinal absorption] ← BOTTLENECK #4
↓
Methionine → MAT (Methionine Adenosyl Transferase) → SAM (S-adenosylmethionine)

SAM → UNIVERSAL METHYL DONOR → methylates:
- DNA (epigenetic silencing) → gene expression regulation
- Histones (H3K4me3, H3K27me3) → chromatin remodeling
- Neurotransmitters (serotonin → melatonin; norepinephrine → epinephrine) ← COMT connection!
- Phosphatidylcholine (membrane integrity)
- Creatine (energy)
↓
SAH (S-adenosylhomocysteine) → Homocysteine
↓ (if cycle blocked: Homocysteine ACCUMULATES → cardiovascular damage, NLRP3 activation)
↓ (if cycle intact: Homocysteine → Cystathionine → Cysteine → Glutathione)

TARGET HOMOCYSTEINE for Mihai: <9 µmol/L (from estimated 12-14 with MTHFR/MTRR)
FIX: 5-MTHF 800mcg + MetilB12 1000mcg sublingual (bypasses FUT2 + MTRR bottlenecks)
```

## Category 4: Sports Performance Diagrams

### Athletic Genotype Profile
```
Create a sports performance genetic profile visual for this patient:

POWER/SPRINT (HIGH ADVANTAGE - green):
ACTN3 R577X RR genotype: Alpha-actinin-3 FULLY expressed in fast-twitch fibers
Mechanism: Stabilizes Z-disc during explosive contractions → efficient force transmission
Evidence: Yang 2003 (AJHG): R allele overrepresented in elite sprinters/power athletes (OR=1.89)
Recommendation: Resistance training 3×/week, plyometrics, <6 rep sets, creatine 5g/day

CARDIOVASCULAR EFFICIENCY (MODERATE):
ACE I/D genotype: balanced profile
I allele → lower ACE → lower angiotensin II → less vasoconstriction → endurance advantage
D allele → higher ACE → more hypertrophy signal → strength advantage
Recommendation: Responds well to both endurance and strength training

NITRIC OXIDE (MODERATE DEFICIT):
NOS3 Glu298Asp rs1799983 (gnomAD 75.1%!): very common but functional
Asp298 → increased NOS3 proteolytic cleavage → reduced eNOS → reduced NO → reduced vasodilation
Impact: VO2max potential slightly reduced, slower recovery between sets
Fix: L-Citrulline 3-6g pre-workout → endogenous arginine → NOS3 substrate → more NO despite reduced enzyme
Also: Beet root juice 400ml (dietary nitrates → NO via independent pathway)

MITOCHONDRIAL BIOGENESIS:
PPARGC1A (PGC-1α): Master regulator of mitochondrial biogenesis
HIIT activates: AMPK → PGC-1α → NRF1/NRF2 → TFAM → mtDNA replication → more mitochondria
For Mihai: SOD2 deficit + SIRT3 intact → HIIT creates hormetic ROS pulse → upregulates antioxidant genes AND increases mitochondrial count
Target: VO2max >45 ml/kg/min at 12 months (from estimated ~38-40)

WEEKLY PROTOCOL VISUAL (calendar-style):
Monday: HIIT Sprint 25min @ 90% FCmax [ACTN3 + BDNF + AMPK + FOXO3]
Tuesday: Upper Resistance 45min @ 70-80% 1RM [ACTN3 RR power phenotype]
Wednesday: Zone 2 Cardio 45min @ 60-70% FCmax [FOXO3 + lipid oxidation + APOE management]
Thursday: HIIT Tabata 20-25min @ 85-95% FCmax [autophagy + PGC-1α + hormesis]
Friday: Lower + Core 45min @ 70-80% 1RM
Saturday: Zone 2 + Mobility 60min [parasympathetic + VitD synthesis]
Sunday: Active Recovery [HRV monitoring target: >55ms]
```

## Category 5: Therapy & Longevity Technology Diagrams

### HBOT Mechanism Visual
```
Create an infographic for Hyperbaric Oxygen Therapy (HBOT) mechanism:

PROCEDURE:
Patient in chamber → 2.0-2.4 ATA pressure → 90-100% O2 (vs 21% ambient)
→ Dissolved oxygen in plasma ↑ 10-15× → O2 reaches poorly vascularized tissues

MECHANISMS (draw as cascade):
1. Hyperoxia pulse → brief ROS → HORMESIS → antioxidant gene upregulation (SOD2, CAT)
   [For Mihai: compensates for SOD2 Val/Val via transcriptional upregulation]
2. Increased O2 → stem cell mobilization from bone marrow ↑ 8× (Thom 2006)
3. Anti-inflammatory: hypoxia-inducible factor HIF-1α regulation → reduced NLRP3 baseline
4. Telomere elongation: Hachmo 2020 key data

KEY STUDY — HACHMO 2020 (cite prominently):
Title: "Hyperbaric oxygen therapy increases telomere length and decreases immunosenescence"
Journal: Aging (Albany NY), 2020
Design: 35 adults >64 years, 60 HBOT sessions × 90 min at 2 ATA
Results:
• T-cell telomeres INCREASED 20-38% (significantly longer)
• B-cell senescence decreased 25%
• T-helper senescence decreased 37.3%
• CD28null T-cells (senescent) decreased 11-37%
• No serious adverse events

FOR MIHAI SPECIFICALLY:
• SOD2 TT → HBOT upregulates SOD2 transcription (hormesis)
• TERT AC → HBOT + TERT = synergistic telomere maintenance
• NLRP3 GoF → HBOT reduces senescent cell burden → less SASP → less NLRP3 activation

PROTOCOL (Shai Efrati, Tel Aviv University):
20 sessions × 90 min × 2.0-2.4 ATA × 5 days/week for 4 weeks
Annual protocol: 1× per year for longevity maintenance

AVAILABLE IN ROMANIA: Bucharest (Spitalul Militar, private centers), Cluj-Napoca
Estimated cost: 100-200 RON/session
```

### Peptide Protocol Mechanism Cards
```
Create 5 mechanism cards for the peptide protocol:

CARD 1 — BPC-157:
Name: Body Protection Compound-157
Structure: 15 amino acids (Gly-Glu-Pro-Pro-Pro-Gly-Lys-Pro-Ala-Asp-Asp-Ala-Gly-Leu-Val)
Origin: Gastric juice protective protein fragment
Mechanism: FAK-paxillin pathway → fibroblast migration; GHSR upregulation → GH receptor;
           eNOS upregulation → NO production (compensates NOS3 Asp298 in Mihai)
Gene targets for Mihai: NOS3 (eNOS compensation), FUT2 (gut mucosal protection → B12 absorption)
Dose: 250-500 mcg/day SC or oral, 4 weeks ON / 2 OFF
Evidence: Sikiric 2016 (J Physiol Paris) — phase II anti-inflammatory effects

CARD 2 — GHK-Cu:
Name: Copper Peptide (Glycyl-L-Histidyl-L-Lysine:Cu2+)
Natural levels: 200 ng/mL at age 20 → 80 ng/mL at age 60
Mechanism: Pickart & Margolina 2018: resets 1002 gene expressions toward "younger" state;
           activates SOD2 transcription, COL1A1, MMP2, BDNF; DNA repair activation
Gene targets: SOD2 (partial transcriptional compensation for Val/Val deficit!), BDNF, collagen genes
Dose: Topic daily + SC 200mcg 3×/week, 30-day cycles

CARD 3 — Epitalon:
Name: Tetrapeptide Ala-Glu-Asp-Gly (pineal extract)
Origin: Khavinson institute, St. Petersburg, Russia
Mechanism: Telomerase (TERT) activator → telomere elongation; regulates pineal melatonin synthesis;
           activates FOXO3 expression
Gene targets: TERT AC (amplifies partial activity), FOXO3 GG (further activation)
Evidence: Khavinson 2002: 2.5× telomerase activity; Goncharova 2010: +16.3% lifespan mice
Dose: 10 mg/day × 10 days, twice yearly

CARD 4 — MOTS-c:
Name: Mitochondrial ORF of 12S rRNA-c (16 amino acids)
Origin: Encoded in MITOCHONDRIAL DNA (unique — not nuclear)
Mechanism: Translocates to nucleus under metabolic stress → activates AMPK → FOXO3 pathway;
           increases insulin sensitivity; activates PGC-1α → mitochondrial biogenesis
Gene targets: SIRT3 (NAD+ synergy), SOD2 (via FOXO3 upregulation), TCF7L2 (insulin sensitivity)
Evidence: Lee 2015 Cell Metabolism: prevents obesity, improves insulin sensitivity +50% in HFD mice
Dose: 5-10 mg SC 1-2×/week

CARD 5 — Thymosin Alpha-1:
Name: Tα1 (Zadaxin) — 28 amino acids
Origin: Thymus gland
Mechanism: TLR2/TLR9 signaling → dendritic cell maturation → T-cell education;
           upregulates IL-10 (anti-inflammatory cytokine) — directly compensates IL10 AG deficit in Mihai!
Gene targets: IL10 AG (boosts IL-10 production), NLRP3 (modulates innate immunity downstream)
Approved: Italy, Singapore, Taiwan for hepatitis B/C; 35+ countries
Dose: 1.6 mg SC × 2/week, 12-week cycle
```

## Usage Notes

- All prompts work with both CLI (`notebooklm generate --prompt "..."`) and Python API
- Upload the client's HTML report as source first, then use these prompts
- For best results, set language to Romanian: `notebooklm language set ro`
- Audio generation takes 2-5 minutes; other diagrams 1-2 minutes
- Download artifacts: `notebooklm download <type> --output /path/`
