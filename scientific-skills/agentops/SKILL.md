---
name: agentops
description: AgentOps is an AI agent monitoring and observability platform that tracks sessions, costs, and events for LLM-powered agents. Use this skill when you need to monitor AI agent workflows in scientific research, debug multi-agent pipelines, or track LLM API costs across experiments.
license: MIT
metadata:
    skill-author: K-Dense Inc.
---

# AgentOps

## Overview

AgentOps is a comprehensive observability platform designed specifically for AI agents. It provides real-time monitoring of agent sessions, automatic tracking of LLM API calls, cost accounting, and debugging tools for multi-agent workflows. In scientific research contexts, AgentOps enables researchers to audit how AI agents process data, identify bottlenecks in automated pipelines, and maintain reproducibility by logging all agent decisions and tool calls.

AgentOps integrates natively with major LLM frameworks including OpenAI, Anthropic, LangChain, AutoGen, CrewAI, and the Google Agent Development Kit (ADK), making it straightforward to add observability to existing research agent workflows without major refactoring.

## When to Use This Skill

Use AgentOps when you need to:

- Monitor AI agents that automate literature review, hypothesis generation, or data analysis
- Track and control LLM API costs across long-running scientific computing jobs
- Debug multi-agent systems where agents collaborate on complex research tasks
- Create audit trails for AI-assisted research to support reproducibility
- Analyze agent performance metrics such as latency, token usage, and error rates
- Replay and inspect individual agent sessions to understand decision paths

## Core Capabilities

- **Session Tracking**: Automatically captures every LLM call, tool invocation, and agent action within a named session
- **Cost Monitoring**: Real-time token and cost accounting per session, per experiment, and across projects
- **Multi-Agent Support**: Tracks interactions between multiple cooperative agents, linking parent and child sessions
- **Error Detection**: Surfaces exceptions, hallucinations, and unexpected terminations with full context
- **Dashboard**: Web-based UI for browsing sessions, replaying runs, and comparing experiments
- **Tags and Metadata**: Attach arbitrary metadata to sessions for filtering and grouping by experiment ID, dataset version, or researcher name
- **Integrations**: Works with OpenAI, Anthropic, LangChain, AutoGen, CrewAI, Google ADK, and more

## Installation and Setup

Install AgentOps using pip:

```bash
pip install agentops
```

For use with specific frameworks, install optional extras:

```bash
# For LangChain integration
pip install agentops langchain

# For Google ADK integration
pip install agentops google-adk
```

Obtain your API key from the AgentOps dashboard at https://app.agentops.ai. Set it as an environment variable for security:

```bash
export AGENTOPS_API_KEY="your_api_key_here"
```

## Using This Skill

### Basic Session Initialization

Initialize AgentOps at the start of your research script. All subsequent LLM calls are automatically captured:

```python
import agentops
import os

# Initialize with API key from environment
agentops.init(os.environ.get("AGENTOPS_API_KEY"))

# Your agent code runs here — all LLM calls are tracked automatically
from openai import OpenAI
client = OpenAI()

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Summarize this protein structure paper."}]
)

agentops.end_session("Success")
```

### Tagging Sessions for Scientific Experiments

Attach metadata to sessions to organize runs by experiment, dataset, or hypothesis:

```python
import agentops

agentops.init(
    api_key=os.environ.get("AGENTOPS_API_KEY"),
    tags=["genomics", "variant-calling", "experiment-42"],
    default_tags={"researcher": "jane_doe", "dataset_version": "v3.1"}
)

# Run your scientific agent workflow
run_variant_analysis_agent()

agentops.end_session("Success")
```

### Tracking Custom Events in Research Pipelines

Log domain-specific events alongside automatic LLM tracking:

```python
import agentops
from agentops import record_action

agentops.init(os.environ.get("AGENTOPS_API_KEY"))

@record_action("protein_folding_prediction")
def predict_structure(sequence: str) -> dict:
    """Predict protein structure and log the action to AgentOps."""
    result = run_alphafold(sequence)
    return {"pdb_id": result.pdb_id, "confidence": result.plddt_score}

# This call is automatically logged as a named action
structure = predict_structure("MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQAPILSRVGDGTQDNLSGAEKAVQVKVKALPDAQFEVVHSLAKWKRQTLGQHDFSAGEGLYTHMKALRPDEDRLSPLHSVYVDQWDWERVMGDGERQFSTLKSTVEAIWAGIKATEAAVSEEFGLAPFLPDQIHFVHSQELLSRYPDLDAKGRERAIAKDLGAVFLVGIGGKLSDGHRHDVRAPDYDDWSTPSELGHAGLNGDILVWNPVLEDAFELSSMGIRVDADTLKHQLALTGDEDRLELEWHQALLRGEMPQTIGGGIGQSRLTMLLLQLPHIGQVQAGVWPAAVRESVPSLL")
print(f"Predicted structure confidence: {structure['confidence']}")

agentops.end_session("Success")
```

### Multi-Agent Research Workflows

Track parent and child agent sessions for collaborative research pipelines:

```python
import agentops

agentops.init(os.environ.get("AGENTOPS_API_KEY"), tags=["multi-agent", "drug-discovery"])

# Parent agent coordinates child agents for different analysis tasks
def run_drug_discovery_pipeline(compound_smiles: str):
    # Child sessions inherit parent context
    with agentops.start_session(tags=["literature-search"]) as lit_session:
        relevant_papers = literature_agent.search(compound_smiles)

    with agentops.start_session(tags=["toxicity-prediction"]) as tox_session:
        toxicity = toxicity_agent.predict(compound_smiles)

    with agentops.start_session(tags=["binding-affinity"]) as bind_session:
        affinity = docking_agent.compute(compound_smiles)

    return {
        "papers": relevant_papers,
        "toxicity": toxicity,
        "binding_affinity": affinity
    }

results = run_drug_discovery_pipeline("CC(=O)Oc1ccccc1C(=O)O")
agentops.end_session("Success")
```

### Google ADK Integration

AgentOps provides an official integration with the Google Agent Development Kit (ADK):

```python
import agentops
from google.adk.agents import Agent
from google.adk.tools import tool

agentops.init(os.environ.get("AGENTOPS_API_KEY"), tags=["google-adk", "scientific-agent"])

@tool
def fetch_pubmed_abstracts(query: str, max_results: int = 10) -> list[dict]:
    """Fetch abstracts from PubMed for a given research query."""
    # Tool calls are automatically tracked by AgentOps
    return pubmed_search(query, max_results)

research_agent = Agent(
    model="gemini-2.0-flash",
    name="research_assistant",
    instruction="You are a scientific research assistant. Help researchers find and summarize relevant literature.",
    tools=[fetch_pubmed_abstracts]
)

# AgentOps automatically traces all ADK agent interactions
response = research_agent.run("Find recent papers on CRISPR base editing efficiency.")
agentops.end_session("Success")
```

### Cost Monitoring for Research Budgets

Monitor and alert on LLM costs to stay within research computing budgets:

```python
import agentops

agentops.init(
    api_key=os.environ.get("AGENTOPS_API_KEY"),
    tags=["cost-tracking", "nightly-analysis"]
)

# Run your analysis — costs are accumulated automatically
run_large_scale_literature_review()

# End session and retrieve cost summary
session = agentops.end_session("Success")
print(f"Total tokens used: {session.token_cost.prompt_tokens + session.token_cost.completion_tokens}")
print(f"Estimated cost: ${session.token_cost.cost:.4f}")
```

## Additional Resources

- AgentOps documentation: https://docs.agentops.ai
- AgentOps dashboard: https://app.agentops.ai
- GitHub repository: https://github.com/AgentOps-AI/agentops
- Google ADK integration guide: https://docs.agentops.ai/v1/integrations/google_adk
