---
name: phoenix-arize
description: Phoenix by Arize is an open source AI observability platform for tracing LLM applications, evaluating AI responses, and analyzing embedding spaces. Use this skill when you need end-to-end visibility into LLM-powered research agents, want to evaluate response quality with automated metrics, or need to inspect embedding clusters from scientific language models.
license: Elastic License 2.0
metadata:
    skill-author: K-Dense Inc.
---

# Phoenix (by Arize)

## Overview

Phoenix is an open source AI observability tool developed by Arize AI. It provides tracing for LLM applications via OpenTelemetry, evaluations of LLM outputs using automated metrics, and embedding analysis for understanding how models represent data. Unlike cloud-only monitoring solutions, Phoenix can run entirely locally — making it suitable for sensitive scientific data that cannot leave an institutional network.

Phoenix is particularly well-suited for scientific research environments where researchers build LLM-powered agents to process literature, reason about experimental data, or automate analysis pipelines. Its evaluation framework enables systematic quality assessment of AI outputs, which is critical for validating AI-assisted research conclusions. Phoenix integrates with LangChain, LlamaIndex, OpenAI, Anthropic, DSPy, and the Google Agent Development Kit (ADK).

## When to Use This Skill

Use Phoenix when you need to:

- Trace every LLM call, tool invocation, and retrieval step in a research agent workflow
- Evaluate LLM response quality using automated metrics such as hallucination detection, relevance, and toxicity
- Analyze embedding representations from scientific language models to detect clustering artifacts or distribution shifts
- Run AI observability entirely on-premise for HIPAA, GDPR, or institutional data governance compliance
- Build evaluation datasets from production traces to guide prompt and model improvements
- Identify slow or expensive steps in multi-step scientific reasoning chains

## Core Capabilities

- **LLM Tracing**: OpenTelemetry-compatible trace collection covering spans for LLM calls, retrievals, embeddings, and tool calls
- **Automated Evaluations**: Built-in evaluators for hallucination, Q&A correctness, relevance, toxicity, and custom criteria using LLM-as-a-judge
- **Embedding Projector**: UMAP-based 2D/3D visualization of embedding vectors to inspect model representations and data clusters
- **Dataset Management**: Create labeled evaluation datasets from traces for benchmarking prompt changes
- **Experiment Comparison**: Run and compare multiple prompt or model configurations against the same dataset
- **Local Deployment**: Runs as a local web application — no data leaves your environment
- **OpenTelemetry Integration**: Works with any OTel-compatible instrumentation library

## Installation and Setup

Install the Phoenix server and OpenTelemetry integration:

```bash
pip install arize-phoenix
pip install arize-phoenix-otel
```

For framework-specific auto-instrumentation:

```bash
# OpenAI instrumentation
pip install openinference-instrumentation-openai

# LangChain instrumentation
pip install openinference-instrumentation-langchain

# Google ADK instrumentation
pip install openinference-instrumentation-google-adk
```

## Using This Skill

### Launching Phoenix and Registering the Tracer

Start a local Phoenix instance and configure OpenTelemetry to send traces to it:

```python
import phoenix as px
from phoenix.otel import register

# Launch Phoenix UI on http://localhost:6006
session = px.launch_app()

# Register a tracer provider pointing at the local Phoenix instance
tracer_provider = register(
    project_name="genomics-research-agent",
    endpoint="http://localhost:6006/v1/traces"
)

print(f"Phoenix UI: {session.url}")
```

### Auto-Instrumenting OpenAI Calls in Research Pipelines

Automatically capture all OpenAI API calls without modifying application code:

```python
import phoenix as px
from phoenix.otel import register
from openinference.instrumentation.openai import OpenAIInstrumentor
from openai import OpenAI

session = px.launch_app()
tracer_provider = register(project_name="literature-synthesis-agent")

# Instrument OpenAI — all subsequent API calls are traced automatically
OpenAIInstrumentor().instrument(tracer_provider=tracer_provider)

client = OpenAI()

def synthesize_abstracts(abstracts: list[str]) -> str:
    """Synthesize findings from a list of research abstracts."""
    combined = "\n\n".join(abstracts)
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[
            {"role": "system", "content": "You are an expert scientific synthesizer."},
            {"role": "user", "content": f"Synthesize these abstracts:\n{combined}"}
        ]
    )
    return response.choices[0].message.content

# This call and all LLM interactions within it are traced in Phoenix
summary = synthesize_abstracts(paper_abstracts)
```

### Tracing LangChain RAG Pipelines for Literature Search

Instrument a retrieval-augmented generation pipeline for scientific question answering:

```python
import phoenix as px
from phoenix.otel import register
from openinference.instrumentation.langchain import LangChainInstrumentor
from langchain.chains import RetrievalQA
from langchain_openai import ChatOpenAI
from langchain_community.vectorstores import Chroma

session = px.launch_app()
tracer_provider = register(project_name="pubmed-qa-agent")
LangChainInstrumentor().instrument(tracer_provider=tracer_provider)

# Build a RAG chain over a PubMed abstract vector store
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
vectorstore = Chroma(persist_directory="./pubmed_abstracts", embedding_function=embeddings)

qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=vectorstore.as_retriever(search_kwargs={"k": 5}),
    return_source_documents=True
)

# Phoenix traces retrieval spans AND LLM spans automatically
result = qa_chain.invoke("What are the mechanisms of CRISPR base editing off-target effects?")
print(result["result"])
```

### Running LLM Evaluations on Research Agent Outputs

Use Phoenix's built-in evaluators to score agent outputs for hallucination and relevance:

```python
import phoenix as px
from phoenix.evals import HallucinationEvaluator, QAEvaluator, OpenAIModel, run_evals
import pandas as pd

# Retrieve traces from the Phoenix session
client = px.Client()
traces_df = client.get_spans_dataframe(project_name="pubmed-qa-agent")

# Prepare evaluation inputs
eval_df = traces_df[["input.value", "output.value", "retrieval.documents"]].rename(columns={
    "input.value": "question",
    "output.value": "answer",
    "retrieval.documents": "context"
}).dropna()

eval_model = OpenAIModel(model="gpt-4o-mini")

# Run hallucination and Q&A correctness evaluations
hallucination_eval, qa_eval = run_evals(
    dataframe=eval_df,
    evaluators=[
        HallucinationEvaluator(eval_model),
        QAEvaluator(eval_model)
    ],
    provide_explanation=True
)

print(f"Hallucination rate: {(hallucination_eval['label'] == 'hallucinated').mean():.1%}")
print(f"Q&A correctness: {(qa_eval['label'] == 'correct').mean():.1%}")
```

### Analyzing Protein Embedding Spaces

Use Phoenix to visualize and inspect embedding clusters from a protein language model:

```python
import phoenix as px
import pandas as pd
import numpy as np

# Assume protein_embeddings is a (N, D) array from ESM-2 or similar
embedding_df = pd.DataFrame({
    "protein_id": protein_ids,
    "function_class": function_labels,
    "organism": organism_labels,
    "embedding": [emb.tolist() for emb in protein_embeddings]
})

# Log embeddings to Phoenix for interactive UMAP visualization
schema = px.Schema(
    prediction_id_column_name="protein_id",
    prediction_label_column_name="function_class",
    embedding_feature_column_names={
        "protein_embedding": px.EmbeddingColumnNames(
            vector_column_name="embedding",
            raw_data_column_name="protein_id"
        )
    },
    tag_column_names=["organism"]
)

dataset = px.Dataset(embedding_df, schema, name="ESM2-protein-embeddings")
session = px.launch_app(primary=dataset)
```

### Google ADK Integration

Instrument Google ADK agents with Phoenix tracing for end-to-end research pipeline observability:

```python
import phoenix as px
from phoenix.otel import register
from openinference.instrumentation.google_adk import GoogleADKInstrumentor
from google.adk.agents import Agent
from google.adk.tools import tool

session = px.launch_app()
tracer_provider = register(project_name="scientific-adk-agent")

# Instrument Google ADK — all agent spans, tool calls, and LLM interactions are traced
GoogleADKInstrumentor().instrument(tracer_provider=tracer_provider)

@tool
def search_chemical_database(compound_name: str) -> dict:
    """Search PubChem for compound properties."""
    return pubchem_api.get_compound(compound_name)

chemistry_agent = Agent(
    model="gemini-2.0-flash",
    name="chemistry_assistant",
    instruction="Answer questions about chemical compounds using the database tool.",
    tools=[search_chemical_database]
)

# Full trace (agent reasoning + tool call + LLM response) captured in Phoenix
response = chemistry_agent.run("What is the molecular weight of caffeine?")
print(f"View traces at: {session.url}")
```

## Additional Resources

- Phoenix documentation: https://docs.arize.com/phoenix
- Phoenix GitHub: https://github.com/Arize-ai/phoenix
- OpenInference instrumentation: https://github.com/Arize-ai/openinference
- Evaluation guide: https://docs.arize.com/phoenix/evaluation/overview
