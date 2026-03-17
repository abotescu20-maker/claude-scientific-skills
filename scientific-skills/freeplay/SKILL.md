---
name: freeplay
description: Freeplay is an LLM prompt management and evaluation platform for versioning prompts, running A/B tests, and managing evaluation datasets. Use this skill when you need to systematically manage and iterate on prompts for research AI applications, evaluate AI output quality against labeled datasets, or collaborate with a team on prompt engineering for scientific workflows.
license: Proprietary
metadata:
    skill-author: K-Dense Inc.
---

# Freeplay

## Overview

Freeplay is a prompt management and LLM evaluation platform that provides a centralized workspace for teams building AI applications. It offers prompt versioning and templates, A/B testing of prompt variants, evaluation datasets and scoring, and production monitoring of LLM calls. For scientific research teams, Freeplay addresses the challenge of managing evolving prompts across multiple researchers and projects — ensuring that prompt changes are tracked, evaluated, and auditable.

Scientific research contexts often require careful prompt engineering: extracting structured data from papers, summarizing clinical notes, interpreting experimental results, or reasoning about molecular data. Freeplay enables teams to treat prompts as first-class versioned artifacts, measure their quality against curated evaluation sets, and collaborate on improvements without losing the history of what was tried. Freeplay integrates with OpenAI, Anthropic, and the Google Agent Development Kit (ADK).

## When to Use This Skill

Use Freeplay when you need to:

- Version and manage prompts used in scientific AI workflows across multiple researchers and projects
- A/B test different prompt formulations to identify which produces higher-quality scientific outputs
- Build and maintain labeled evaluation datasets for assessing AI assistant quality on domain-specific tasks
- Monitor LLM call quality in production research pipelines with session-level tracing
- Centralize prompt templates used across different parts of a research platform (literature search, data extraction, hypothesis generation)
- Collaborate on prompt development with domain scientists who are not software engineers

## Core Capabilities

- **Prompt Versioning**: Store and version prompt templates with variables, enabling rollback and history tracking
- **Template Variables**: Define parameterized prompt templates that can be called with dynamic inputs at runtime
- **A/B Testing**: Route a fraction of traffic to prompt variants and compare output quality metrics
- **Evaluation Datasets**: Upload curated input/output pairs and run batch evaluations against prompt templates
- **Session Logging**: Log LLM inputs, outputs, latency, and token usage to Freeplay for monitoring
- **Scoring**: Apply human or automated scores to logged completions to build quality metrics over time
- **Team Collaboration**: Share prompts and evaluation results across a research team through a web dashboard
- **Environment Promotion**: Manage prompts across development, staging, and production environments

## Installation and Setup

Install the Freeplay Python SDK:

```bash
pip install freeplay
```

Retrieve your API key and base URL from the Freeplay dashboard at https://app.freeplay.ai. Store credentials securely:

```bash
export FREEPLAY_API_KEY="your_api_key_here"
export FREEPLAY_BASE_URL="https://app.freeplay.ai/api"
```

## Using This Skill

### Initializing the Freeplay Client

```python
import os
from freeplay import Freeplay

client = Freeplay(
    freeplay_api_key=os.environ.get("FREEPLAY_API_KEY"),
    base_url=os.environ.get("FREEPLAY_BASE_URL", "https://app.freeplay.ai/api")
)
```

### Fetching and Using Versioned Prompt Templates

Retrieve a prompt template by name and use it with dynamic variables at runtime:

```python
import os
from freeplay import Freeplay

client = Freeplay(
    freeplay_api_key=os.environ.get("FREEPLAY_API_KEY"),
    base_url="https://app.freeplay.ai/api"
)

# Fetch the latest version of a prompt template for literature summarization
prompt_template = client.prompts.get(
    project_id="genomics-research",
    template_name="abstract-summarizer",
    environment="production"
)

# Fill in template variables with concrete values for a specific paper
formatted_messages = prompt_template.format(variables={
    "abstract": "We report a novel CRISPR base editor achieving 89% efficiency...",
    "target_audience": "molecular biologists",
    "output_format": "three bullet points"
})

# Use the formatted prompt with your LLM of choice
import openai
response = openai.chat.completions.create(
    model=prompt_template.llm_parameters.model,
    messages=formatted_messages,
    temperature=prompt_template.llm_parameters.temperature
)

print(response.choices[0].message.content)
```

### Logging Sessions and Completions for Monitoring

Log production LLM calls to Freeplay for quality monitoring and analysis:

```python
import os
import uuid
from freeplay import Freeplay
from openai import OpenAI

freeplay_client = Freeplay(
    freeplay_api_key=os.environ.get("FREEPLAY_API_KEY"),
    base_url="https://app.freeplay.ai/api"
)
openai_client = OpenAI()

def extract_gene_mentions(text: str, session_id: str = None) -> dict:
    """Extract gene mentions from biomedical text and log to Freeplay."""
    if session_id is None:
        session_id = str(uuid.uuid4())

    prompt_template = freeplay_client.prompts.get(
        project_id="biomedical-nlp",
        template_name="gene-mention-extractor",
        environment="production"
    )

    formatted = prompt_template.format(variables={"text": text})
    session_info = prompt_template.session_info(session_id=session_id)

    response = openai_client.chat.completions.create(
        model=prompt_template.llm_parameters.model,
        messages=formatted,
        temperature=0.0
    )

    completion_text = response.choices[0].message.content

    # Log the call to Freeplay for monitoring
    freeplay_client.recordings.log(
        session_info=session_info,
        inputs={"text": text},
        output=completion_text,
        prompt_version_info=prompt_template.prompt_version_info
    )

    return {"session_id": session_id, "gene_mentions": completion_text}

# Batch process abstracts
for abstract in pubmed_abstracts:
    result = extract_gene_mentions(abstract)
    print(result["gene_mentions"])
```

### Running A/B Tests on Scientific Prompts

Route traffic between two prompt variants to compare extraction quality:

```python
import os
import random
from freeplay import Freeplay
from openai import OpenAI

freeplay_client = Freeplay(
    freeplay_api_key=os.environ.get("FREEPLAY_API_KEY"),
    base_url="https://app.freeplay.ai/api"
)
openai_client = OpenAI()

def run_ab_test_extraction(clinical_text: str, session_id: str) -> dict:
    """Run A/B test between two clinical entity extraction prompts."""
    # Randomly assign to variant A or B (50/50 split)
    variant = "clinical-ner-v1" if random.random() < 0.5 else "clinical-ner-v2"

    prompt_template = freeplay_client.prompts.get(
        project_id="clinical-ai",
        template_name=variant,
        environment="production"
    )

    formatted = prompt_template.format(variables={"clinical_text": clinical_text})
    session_info = prompt_template.session_info(session_id=session_id)

    response = openai_client.chat.completions.create(
        model=prompt_template.llm_parameters.model,
        messages=formatted,
        temperature=0.0
    )

    output = response.choices[0].message.content

    freeplay_client.recordings.log(
        session_info=session_info,
        inputs={"clinical_text": clinical_text},
        output=output,
        prompt_version_info=prompt_template.prompt_version_info
    )

    return {"variant": variant, "entities": output}

# Process clinical notes with A/B testing
for note in clinical_notes:
    result = run_ab_test_extraction(note["text"], note["note_id"])
```

### Scoring Completions for Evaluation

Submit human or automated quality scores for logged completions:

```python
from freeplay import Freeplay

freeplay_client = Freeplay(
    freeplay_api_key=os.environ.get("FREEPLAY_API_KEY"),
    base_url="https://app.freeplay.ai/api"
)

# After domain experts review outputs, submit scores programmatically
def submit_expert_scores(session_id: str, accuracy: float, completeness: float):
    """Submit expert evaluation scores to Freeplay."""
    freeplay_client.feedback.log(
        session_id=session_id,
        scores={
            "factual_accuracy": accuracy,   # 0.0 to 1.0
            "completeness": completeness     # 0.0 to 1.0
        }
    )

# Example: submit scores after pathologist reviews AI summaries
reviewed_sessions = [
    ("session_001", 0.95, 0.88),
    ("session_002", 0.72, 0.91),
    ("session_003", 0.88, 0.79)
]

for session_id, accuracy, completeness in reviewed_sessions:
    submit_expert_scores(session_id, accuracy, completeness)
    print(f"Scores submitted for {session_id}")
```

### Google ADK Integration

Manage prompts for Google ADK agents using Freeplay's versioned template system:

```python
import os
import uuid
from freeplay import Freeplay
from google.adk.agents import Agent
from google.adk.tools import tool
from openai import OpenAI

freeplay_client = Freeplay(
    freeplay_api_key=os.environ.get("FREEPLAY_API_KEY"),
    base_url="https://app.freeplay.ai/api"
)
openai_client = OpenAI()

@tool
def summarize_paper(doi: str, abstract: str) -> str:
    """Summarize a research paper abstract using a versioned Freeplay prompt."""
    session_id = str(uuid.uuid4())
    template = freeplay_client.prompts.get(
        project_id="literature-review",
        template_name="paper-summarizer",
        environment="production"
    )
    formatted = template.format(variables={"doi": doi, "abstract": abstract})
    session_info = template.session_info(session_id=session_id)

    response = openai_client.chat.completions.create(
        model=template.llm_parameters.model,
        messages=formatted
    )
    summary = response.choices[0].message.content

    # Log to Freeplay for monitoring and evaluation
    freeplay_client.recordings.log(
        session_info=session_info,
        inputs={"doi": doi, "abstract": abstract},
        output=summary,
        prompt_version_info=template.prompt_version_info
    )
    return summary

literature_agent = Agent(
    model="gemini-2.0-flash",
    name="literature_reviewer",
    instruction="Review and summarize scientific papers for researchers.",
    tools=[summarize_paper]
)

response = literature_agent.run("Summarize the abstract from doi:10.1038/nature12373")
```

## Additional Resources

- Freeplay documentation: https://docs.freeplay.ai
- Freeplay dashboard: https://app.freeplay.ai
- Python SDK reference: https://docs.freeplay.ai/python-sdk
- Prompt management guide: https://docs.freeplay.ai/prompt-management
