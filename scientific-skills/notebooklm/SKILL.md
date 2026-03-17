---
name: notebooklm
description: Google NotebookLM automation for generating AI-powered explanatory diagrams, mind maps, infographics, slide decks, and structured reports from ABLE GENETICS genomic reports and scientific documents. Use this skill when you need to: create visual explainers of genomic sections (longevity axes, pathway diagrams, supplement protocols, PhenoAge mechanisms), generate mind maps of gene interactions, create podcast-style audio overviews of reports, build slide decks summarizing genomic findings, or upload and query scientific PDFs/HTML reports. Requires notebooklm-py (pip install notebooklm-py) and Google account login.
license: MIT
metadata:
    skill-author: ABLE GENETICS / Andrei Botescu
---

# NotebookLM — ABLE GENETICS Diagram Generator

Automate Google NotebookLM to generate explanatory visual content from ABLE GENETICS genomic reports. Creates mind maps, infographics, slide decks, and audio overviews of complex genomic sections.

## Quick Start

### Authentication

```bash
# One-time browser login (saves to ~/.notebooklm/storage_state.json)
notebooklm login
```

### Core Workflow for ABLE GENETICS Reports

```bash
# 1. Create notebook for a client
notebooklm create "Mihai Berbece — Genomic Analysis v3.0"

# 2. Add the HTML/PDF report as source
notebooklm source add /Desktop/MBerb/Raport_Mihai_Berbece_v3.0.html --notebook "Mihai"

# 3. Generate diagrams (see sections below)
notebooklm generate mind-map --notebook "Mihai"
notebooklm generate infographic --notebook "Mihai"
notebooklm generate slide-deck --notebook "Mihai"
notebooklm generate audio --notebook "Mihai"
```

## ABLE GENETICS — Standard Diagram Workflows

### 1. Gene Interaction Mind Map

Generate a mind map showing how all genes interact in Mihai's profile:

```bash
# Generate mind map focused on longevity axes
notebooklm generate mind-map \
  --notebook "Mihai Berbece" \
  --prompt "Create a detailed mind map showing: (1) FOXO3 at center, connected to SOD2, SIRT3, NLRP3, IL10 through the oxidative stress → inflammaging → longevity axis. (2) APOE E4 connected to cardiovascular risk pathways. (3) TCF7L2 connected to metabolic risk. (4) MTHFR connected to methylation cycle. Show activation (+) and inhibition (-) relationships."
```

### 2. PhenoAge Mechanism Infographic

```bash
notebooklm generate infographic \
  --notebook "Mihai Berbece" \
  --prompt "Create an infographic explaining PhenoAge calculation: show 9 biomarkers (Albumin 4.3, Creatinine 0.87, Glucose 87.5, hsCRP 0.42, Lymphocytes 34.2%, MCV 88.1, RDW 13.8%, ALP 52, WBC 5.8), their weights in the Levine formula, and how each contributes to the final PhenoAge 39.2. Highlight RDW as the biggest driver (−4.68 years potential)."
```

### 3. Supplement Protocol Slide Deck

```bash
notebooklm generate slide-deck \
  --notebook "Mihai Berbece" \
  --prompt "Create a slide deck for the supplement protocol with: Slide 1 — Overview (20 supplements, priority tiers). Slide 2 — Priority 1: MitoQ + CoQ10 (SOD2 pathway). Slide 3 — Priority 1: Omega-3 4g (APOE E4 protocol). Slide 4 — Methylation stack (MetilFolat + MetilB12, MTHFR pathway diagram). Slide 5 — Metabolic stack (Berberina + IF, TCF7L2 AA). Slide 6 — Longevity stack (NMN + Resveratrol, FOXO3 activation). Slide 7 — Peptide protocol (BPC-157, GHK-Cu, Epitalon). Slide 8 — 12-month timeline."
```

### 4. Pathway Diagram — FOXO3 Activation Cascade

```bash
notebooklm generate infographic \
  --notebook "Mihai Berbece" \
  --prompt "Draw the FOXO3 activation cascade as a flow diagram: Exercise/Fasting → AMPK activation → SIRT1 → FOXO3 deacetylation → nuclear translocation → target genes (SOD2 upregulation, CAT, GADD45, PINK1/mitophagy). Also show the suppression pathway: Insulin/IGF-1 → PI3K → AKT → FOXO3 phosphorylation → cytoplasmic retention → inactive. And the inflammaging suppression: NLRP3 GoF → IL-1β → NF-κB → FOXO3 suppression. Mark Mihai's specific variants."
```

### 5. NLRP3 Inflammasome Diagram

```bash
notebooklm generate infographic \
  --notebook "Mihai Berbece" \
  --prompt "Create a molecular diagram of the NLRP3 inflammasome cascade specific to Mihai: Input signals (SOD2 ROS from Val/Val genotype, APOE E4 cholesterol crystals, glucose spikes from TCF7L2 AA) → NLRP3 Gain-of-Function assembly (NLRP3 + ASC + Caspase-1) → IL-1β release + IL-18 → downstream effects (NF-κB, TNF-α amplification via rs1800629, FOXO3 suppression). Show intervention points: Omega-3 EPA (blocks), Quercetin (direct inhibitor), NMN (reduces ROS substrate), IF 16:8."
```

### 6. Vitamin D Metabolism Pathway

```bash
notebooklm generate infographic \
  --notebook "Mihai Berbece" \
  --prompt "Create a vitamin D metabolism pathway diagram: Sun/Diet → 7-dehydrocholesterol → Skin D3 → CYP2R1 (liver, 25-hydroxylation, Mihai has rs10741657 variant) → 25-OH-VitD → CYP27B1 (kidney, 1α-hydroxylation) → 1,25(OH)2D3 → VDR (Mihai: FokI CT rs2228570 = reduced sensitivity) → 2776 genomic targets. Also show GC protein transport step (rs4588 + rs7041 variants reduce transport). Indicate WHY 5000 IU needed vs 2000 IU standard."
```

### 7. Methylation Cycle Diagram

```bash
notebooklm generate infographic \
  --notebook "Mihai Berbece" \
  --prompt "Draw the one-carbon/methylation cycle: Folate (diet) → DHFR → THF → SHMT → 5,10-MTHF → MTHFR (BLOCKED in Mihai: A1298C CT variant) → 5-MTHF → MTR + MTRR (Mihai: AG variant reduces B12 recycling) → Homocysteine → Methionine → SAM (universal methyl donor) → DNA methylation, Histone methylation, Neurotransmitter synthesis. Show consequences of blockage: elevated homocysteine, reduced SAM, epigenetic aging. Show fix: 5-MTHF bypass + MetilB12."
```

### 8. Audio Overview (Podcast) for Client Delivery

```bash
notebooklm generate audio \
  --notebook "Mihai Berbece" \
  --prompt "Generate a conversational podcast overview of Mihai Berbece's genomic report. Cover: (1) What WGS revealed — top 5 findings. (2) The FOXO3 advantage and how to activate it. (3) The SOD2-NLRP3 connection — why mitochondrial support is critical. (4) APOE E4 management strategy. (5) The 12-month action plan. Make it accessible, not overly technical. Duration: 8-10 minutes."
```

## Python API Usage

For programmatic diagram generation from ABLE GENETICS reports:

```python
import asyncio
from notebooklm import NotebookLMClient

async def generate_able_genetics_diagrams(report_path: str, client_name: str):
    """Generate all standard ABLE GENETICS visual content for a client report."""

    async with NotebookLMClient.from_storage() as client:
        # Create notebook
        notebooks = await client.notebooks.list()
        notebook_name = f"ABLE GENETICS — {client_name}"

        # Check if notebook exists
        notebook = next(
            (nb for nb in notebooks if notebook_name in nb.title),
            None
        )

        if not notebook:
            notebook = await client.notebooks.create(title=notebook_name)
            print(f"Created notebook: {notebook.id}")

        # Add report as source
        with open(report_path, 'r') as f:
            report_content = f.read()

        source = await client.sources.add_text(
            notebook_id=notebook.id,
            content=report_content,
            title=f"Raport Genomic {client_name}"
        )
        await client.sources.wait(notebook_id=notebook.id, source_id=source.id)
        print(f"Source indexed: {source.id}")

        # Generate all standard diagrams
        diagrams = {
            "mind_map": {
                "type": "mind-map",
                "prompt": f"Gene interaction mind map for {client_name}: FOXO3 central node connected to all identified genetic axes (Mitochondrial, Inflammatory, Cardiovascular, Metabolic, Longevity, Neurocognitive). Show activation/inhibition relationships and supplement interventions."
            },
            "infographic_phenoage": {
                "type": "infographic",
                "prompt": f"PhenoAge calculator infographic for {client_name}: 9 biomarkers → Levine formula → PhenoAge result. Highlight optimization potential per biomarker."
            },
            "slide_deck": {
                "type": "slide-deck",
                "prompt": f"Executive summary slide deck for {client_name} genomic report. 8 slides covering: genetic profile, top risks, key advantages, supplement protocol, lifestyle optimization, peptide protocols, longevity therapies, 12-month plan."
            },
            "audio": {
                "type": "audio",
                "prompt": f"Podcast overview of {client_name}'s genomic report for patient delivery. 8 minutes, conversational, cover top findings and actionable recommendations."
            }
        }

        artifacts = {}
        for name, config in diagrams.items():
            print(f"Generating {name}...")
            artifact = await client.artifacts.generate(
                notebook_id=notebook.id,
                artifact_type=config["type"],
                instructions=config["prompt"]
            )
            artifact = await client.artifacts.wait(
                notebook_id=notebook.id,
                artifact_id=artifact.id
            )
            artifacts[name] = artifact
            print(f"  Generated: {artifact.id}")

        return artifacts

# Run for Mihai
asyncio.run(generate_able_genetics_diagrams(
    "/Users/andreibotescu/Desktop/MBerb/Raport_Mihai_Berbece_v3.0.html",
    "Mihai Berbece"
))
```

## ABLE GENETICS — Standard Diagram Set

For every client report, generate these 8 standard diagrams:

| Diagram | Type | Purpose |
|---------|------|---------|
| Gene Interaction Web | mind-map | Overview of all gene interactions |
| PhenoAge Breakdown | infographic | Biomarker contributions visual |
| Supplement Protocol | slide-deck | Prioritized supplement stack |
| Methylation Cycle | infographic | MTHFR/MTRR/MTR pathway |
| FOXO3 Activation | infographic | Longevity axis mechanism |
| NLRP3 Cascade | infographic | Inflammaging pathway |
| 12-Month Timeline | infographic | Action plan visual |
| Client Audio | audio | Patient-friendly overview |

## Downloading and Exporting Artifacts

```bash
# List all generated artifacts in a notebook
notebooklm artifact list --notebook "Mihai Berbece"

# Download a specific artifact
notebooklm download infographic --notebook "Mihai" --output /Desktop/MBerb/diagrams/

# Export slide deck to Google Slides
notebooklm artifact export --notebook "Mihai" --artifact-id <id> --to google-slides

# Download audio as MP3
notebooklm download audio --notebook "Mihai" --output /Desktop/MBerb/
```

## Batch Processing Multiple Clients

```python
import asyncio
from notebooklm import NotebookLMClient
from pathlib import Path

async def batch_generate_for_all_clients():
    """Generate diagrams for all ABLE GENETICS clients with reports."""

    report_dir = Path("/Users/andreibotescu/Desktop/ABLE GENETICS/reports")

    async with NotebookLMClient.from_storage() as client:
        for report_path in report_dir.glob("*_v*.html"):
            client_name = report_path.stem.split("_v")[0].replace("_", " ")
            print(f"\nProcessing: {client_name}")

            # Create notebook
            notebook = await client.notebooks.create(
                title=f"ABLE — {client_name} — {report_path.stem}"
            )

            # Add report
            source = await client.sources.add_file(
                notebook_id=notebook.id,
                file_path=str(report_path)
            )
            await client.sources.wait(notebook_id=notebook.id, source_id=source.id)

            # Generate mind map + audio as minimum set
            mind_map = await client.artifacts.generate(
                notebook_id=notebook.id,
                artifact_type="mind-map",
                instructions=f"Complete gene interaction network for {client_name}"
            )

            audio = await client.artifacts.generate(
                notebook_id=notebook.id,
                artifact_type="audio",
                instructions=f"Patient-friendly 8-minute podcast overview for {client_name}"
            )

            print(f"  Queued: mind-map={mind_map.id}, audio={audio.id}")

asyncio.run(batch_generate_for_all_clients())
```

## Adding Scientific Sources to Notebooks

For ABLE GENETICS research contexts:

```bash
# Add PubMed articles (by URL)
notebooklm source add https://pubmed.ncbi.nlm.nih.gov/19706460/ --notebook "FOXO3 Research"

# Add longevity papers
notebooklm source add https://doi.org/10.1073/pnas.0801030105 --notebook "FOXO3 Research"

# Add the HBOT telomere study
notebooklm source add https://doi.org/10.18632/aging.202188 --notebook "HBOT Research"

# Add from local PDF (monographie)
notebooklm source add /Users/andreibotescu/Desktop/MBerb/ABLE_Genetics_Monografie_Mihai.pdf --notebook "Mihai Berbece"
```

## Query Notebooks with Chat

```bash
# Ask questions about the report
notebooklm ask "What is the most urgent supplement to start for this client?" --notebook "Mihai"

notebooklm ask "Explain the FOXO3-NLRP3 interaction in simple terms" --notebook "Mihai"

notebooklm ask "What peptide protocols are recommended and why?" --notebook "Mihai"

notebooklm ask "Summarize the vitamin D pathway deficiencies and corrections" --notebook "Mihai"
```

## Integration with ABLE GENETICS Platform

When integrated with the able-genetics-platform (Next.js + Firebase):

1. After generating HTML report → auto-upload to NotebookLM notebook
2. Generate mind map + slide deck for client delivery portal
3. Store artifact URLs in Firestore (reports collection)
4. Client can access visual explanations alongside the full report

```typescript
// In able-genetics-platform (Next.js API route)
// app/api/reports/[id]/generate-diagrams/route.ts
export async function POST(req: Request) {
  const { reportId } = await req.json()

  // Fetch report from Firestore
  const report = await getReportById(reportId)

  // Trigger NotebookLM diagram generation (via Python subprocess or Cloud Run)
  const result = await fetch('/api/notebooklm/generate', {
    method: 'POST',
    body: JSON.stringify({
      clientName: report.client_name,
      reportHtml: report.html_content,
      diagrams: ['mind-map', 'infographic', 'slide-deck', 'audio']
    })
  })

  return Response.json(await result.json())
}
```

## Notes

- Requires Google account authentication (one-time browser flow)
- Uses undocumented Google RPC APIs — may break with Google updates
- Artifact generation is asynchronous (use `wait` commands)
- Audio generation typically takes 2-5 minutes
- Mind maps and infographics typically take 1-2 minutes
- All generated content is stored in your Google NotebookLM account
- Export to Google Docs/Slides available for slide decks
