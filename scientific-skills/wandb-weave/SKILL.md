---
name: wandb-weave
description: Weights & Biases Weave is an LLM application tracing and evaluation framework for tracking AI workflows, building evaluation pipelines, and iterating on prompts and models. Use this skill when you need to trace and evaluate LLM-powered research agents, build systematic evaluation datasets for scientific AI applications, or monitor function-level performance in AI pipelines.
license: Apache-2.0
metadata:
    skill-author: K-Dense Inc.
---

# Weights & Biases Weave

## Overview

Weave is the LLM observability and evaluation product within the Weights & Biases (W&B) ecosystem. While W&B's core product focuses on ML experiment tracking, Weave extends that capability to LLM applications: tracing chains of function calls, logging model inputs and outputs, managing evaluation datasets, and running systematic evaluations of AI systems. Weave uses a decorator-based approach that makes it straightforward to add tracing to existing Python code without restructuring it.

For scientific research, Weave provides a structured way to track how AI agents process data, generate hypotheses, or summarize literature — and to build repeatable evaluation harnesses that quantify AI assistant quality over time. Weave integrates with OpenAI, Anthropic, Google Gemini, LangChain, LlamaIndex, DSPy, and the Google Agent Development Kit (ADK).

## When to Use This Skill

Use Weave when you need to:

- Trace individual function calls within a scientific AI pipeline to identify errors and latency bottlenecks
- Build evaluation datasets from real research agent runs to benchmark AI quality
- Run systematic evaluations of prompt templates or model configurations against a labeled dataset
- Track LLM call costs and token usage across experiments in a research project
- Iterate on AI-assisted analysis functions with a clear record of what changed and how quality changed
- Collaborate with a team on shared evaluation datasets and model comparison results

## Core Capabilities

- **Op Tracing**: Decorate any Python function with `@weave.op()` to capture inputs, outputs, and nested call hierarchies
- **Automatic LLM Instrumentation**: Auto-traces OpenAI, Anthropic, and Google Gemini calls without code changes
- **Datasets**: Version-controlled datasets of examples with inputs and expected outputs for evaluation
- **Evaluations**: Run a model or pipeline against a dataset and compute metrics with a scorer function
- **Experiment Comparison**: Compare evaluation results across models, prompts, and pipeline versions
- **Call Explorer**: Web UI to browse all traced calls, filter by time or function, and inspect inputs/outputs
- **Leaderboards**: Aggregate evaluation scores across runs for ranking models and prompts

## Installation and Setup

Install Weave:

```bash
pip install weave
```

For W&B integration (recommended for team collaboration):

```bash
pip install weave wandb
```

Authenticate with Weights & Biases:

```bash
wandb login
```

Alternatively, set the API key as an environment variable:

```bash
export WANDB_API_KEY="your_api_key"
```

## Using This Skill

### Initializing Weave for a Research Project

```python
import weave

# Initialize Weave with a project name — all traced calls are stored under this project
weave.init("protein-structure-prediction-agent")
```

### Decorating Research Functions for Tracing

Add `@weave.op()` to any function to capture its inputs, outputs, and execution time:

```python
import weave
from openai import OpenAI

weave.init("genomics-pipeline-tracing")
openai_client = OpenAI()

@weave.op()
def analyze_protein(sequence: str) -> dict:
    """Analyze a protein sequence using an LLM and return structured results."""
    response = openai_client.chat.completions.create(
        model="gpt-4o",
        messages=[
            {"role": "system", "content": "You are an expert in protein biochemistry."},
            {"role": "user", "content": f"Analyze this protein sequence and identify key functional domains: {sequence}"}
        ]
    )
    analysis_text = response.choices[0].message.content
    return {
        "sequence_length": len(sequence),
        "analysis": analysis_text,
        "model_used": "gpt-4o"
    }

@weave.op()
def batch_analyze_proteins(sequences: list[str]) -> list[dict]:
    """Analyze a batch of protein sequences."""
    return [analyze_protein(seq) for seq in sequences]

# Both functions and all nested OpenAI calls are traced automatically
results = batch_analyze_proteins(test_sequences)
```

### Building Evaluation Datasets for Scientific AI

Create versioned evaluation datasets from existing labeled research data:

```python
import weave

weave.init("clinical-note-summarization")

# Create an evaluation dataset from labeled examples
examples = [
    {
        "clinical_note": "Patient presents with acute dyspnea, SpO2 82% on room air. CXR shows bilateral infiltrates consistent with ARDS.",
        "expected_summary": "ARDS with severe hypoxemia requiring ICU-level care.",
        "severity": "critical"
    },
    {
        "clinical_note": "Follow-up for well-controlled type 2 diabetes. HbA1c 6.8%, no complications noted.",
        "expected_summary": "Well-controlled T2DM, routine follow-up.",
        "severity": "stable"
    },
    {
        "clinical_note": "Post-op day 2 from laparoscopic cholecystectomy. Tolerating diet, pain controlled, wound healing well.",
        "expected_summary": "Uncomplicated post-operative recovery.",
        "severity": "stable"
    }
]

dataset = weave.Dataset(name="clinical-note-summaries-v1", rows=examples)
weave.publish(dataset)
print("Dataset published to W&B Weave.")
```

### Running Systematic Evaluations

Define a scorer and evaluate an AI pipeline against the dataset:

```python
import weave
from openai import OpenAI

weave.init("clinical-note-summarization")
client = OpenAI()

@weave.op()
def summarize_clinical_note(clinical_note: str) -> dict:
    """Generate a clinical note summary using GPT-4o."""
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "Summarize clinical notes concisely for physician review."},
            {"role": "user", "content": clinical_note}
        ]
    )
    return {"summary": response.choices[0].message.content}

@weave.op()
def summary_quality_scorer(expected_summary: str, model_output: dict) -> dict:
    """Score summary quality using semantic similarity via LLM judge."""
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "Score how well the generated summary matches the reference on a scale of 0-1."},
            {"role": "user", "content": f"Reference: {expected_summary}\nGenerated: {model_output['summary']}\nReturn only a number between 0 and 1."}
        ]
    )
    try:
        score = float(response.choices[0].message.content.strip())
    except ValueError:
        score = 0.0
    return {"quality_score": min(max(score, 0.0), 1.0)}

# Load published dataset and run evaluation
dataset = weave.ref("clinical-note-summaries-v1").get()
evaluation = weave.Evaluation(dataset=dataset, scorers=[summary_quality_scorer])
results = asyncio.run(evaluation.evaluate(summarize_clinical_note))
print(f"Mean quality score: {results['summary_quality_scorer']['quality_score']['mean']:.3f}")
```

### Tracing Multi-Step Scientific Agent Workflows

Trace a full scientific discovery pipeline with nested operations:

```python
import weave
from openai import OpenAI

weave.init("drug-discovery-pipeline")
client = OpenAI()

@weave.op()
def retrieve_related_compounds(target_protein: str) -> list[str]:
    """Retrieve known compounds targeting a protein from a database."""
    return chembl_api.get_compounds_for_target(target_protein)

@weave.op()
def predict_binding_affinity(compound_smiles: str, target_protein: str) -> float:
    """Predict binding affinity using an ML model."""
    return docking_model.predict(compound_smiles, target_protein)

@weave.op()
def generate_hypothesis(target_protein: str, top_compounds: list[str]) -> str:
    """Generate a research hypothesis using an LLM."""
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[{
            "role": "user",
            "content": f"Given that {', '.join(top_compounds)} bind to {target_protein}, generate a mechanistic hypothesis."
        }]
    )
    return response.choices[0].message.content

@weave.op()
def run_discovery_pipeline(target_protein: str) -> dict:
    """Full drug discovery pipeline — all sub-calls are traced in Weave."""
    compounds = retrieve_related_compounds(target_protein)
    affinities = {c: predict_binding_affinity(c, target_protein) for c in compounds[:10]}
    top_compounds = sorted(affinities, key=affinities.get, reverse=True)[:3]
    hypothesis = generate_hypothesis(target_protein, top_compounds)
    return {"top_compounds": top_compounds, "hypothesis": hypothesis}

result = run_discovery_pipeline("EGFR")
```

### Google ADK Integration

Trace Google ADK agent tool calls and LLM responses with Weave:

```python
import weave
from google.adk.agents import Agent
from google.adk.tools import tool

weave.init("adk-scientific-agent")

@weave.op()
@tool
def fetch_gene_expression_data(gene_symbol: str, tissue: str) -> dict:
    """Fetch gene expression data from GTEx for a given gene and tissue."""
    data = gtex_api.get_expression(gene_symbol, tissue)
    return {
        "gene": gene_symbol,
        "tissue": tissue,
        "median_tpm": data.median_tpm,
        "sample_count": data.n_samples
    }

@weave.op()
@tool
def analyze_expression_pattern(expression_data: dict) -> str:
    """Interpret gene expression levels in biological context."""
    level = "high" if expression_data["median_tpm"] > 10 else "low"
    return f"{expression_data['gene']} shows {level} expression in {expression_data['tissue']} ({expression_data['median_tpm']:.1f} TPM across {expression_data['sample_count']} samples)."

gene_expression_agent = Agent(
    model="gemini-2.0-flash",
    name="gene_expression_analyst",
    instruction="Analyze gene expression patterns across tissues using GTEx data.",
    tools=[fetch_gene_expression_data, analyze_expression_pattern]
)

# ADK agent run is fully traced in Weave — browse at https://wandb.ai
response = gene_expression_agent.run("Compare BRCA1 expression in breast tissue vs. liver.")
```

## Additional Resources

- Weave documentation: https://weave-docs.wandb.ai
- Weave GitHub: https://github.com/wandb/weave
- W&B platform: https://wandb.ai
- Evaluation guide: https://weave-docs.wandb.ai/guides/core-types/evaluations
