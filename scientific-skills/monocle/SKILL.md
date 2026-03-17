---
name: monocle
description: Monocle is an open source GenAI observability library that provides end-to-end tracing for AI applications using OpenTelemetry, with infrastructure-agnostic monitoring that works across any cloud or on-premise deployment. Use this skill when you need vendor-neutral observability for GenAI research pipelines, want to export traces to existing monitoring infrastructure, or require full control over trace data without sending it to third-party SaaS platforms.
license: Apache-2.0
metadata:
    skill-author: K-Dense Inc.
---

# Monocle

## Overview

Monocle is an open source observability library for GenAI and LLM applications, developed under the Apache 2.0 license. It instruments AI frameworks and LLM calls using OpenTelemetry, capturing traces that describe the full chain of operations in a GenAI application — from user input through retrieval, reasoning, and final response. Because Monocle is built on OpenTelemetry, traces can be exported to any compatible backend: Jaeger, Zipkin, Grafana Tempo, AWS X-Ray, Azure Monitor, or a custom OTLP endpoint.

For scientific research environments, Monocle is particularly valuable when researchers need observability for GenAI pipelines but cannot send data to external SaaS platforms due to data governance, HIPAA, or institutional policy. Monocle runs entirely within your infrastructure and integrates with LangChain, LlamaIndex, Haystack, OpenAI, and the Google Agent Development Kit (ADK), making it a neutral layer that works alongside existing research computing infrastructure.

## When to Use This Skill

Use Monocle when you need to:

- Add GenAI observability to research pipelines without sending trace data to external SaaS platforms
- Export LLM traces to existing monitoring infrastructure (Grafana, Jaeger, OpenSearch, AWS CloudWatch)
- Instrument AI workflows across heterogeneous environments: on-premise HPC, cloud VMs, and Kubernetes
- Capture end-to-end traces spanning multiple AI frameworks in a single research pipeline
- Maintain compliance with institutional data governance requirements while still gaining observability
- Integrate GenAI tracing into existing OpenTelemetry-based monitoring stacks

## Core Capabilities

- **OpenTelemetry Native**: All traces are standard OTLP spans compatible with any OTel backend
- **Framework Auto-Instrumentation**: Automatic instrumentation for LangChain, LlamaIndex, Haystack, and OpenAI with minimal code changes
- **Infrastructure Agnostic**: Export traces to any OTLP endpoint — local, cloud, or hybrid
- **Workflow Tracing**: Captures the full GenAI workflow as a tree of spans including LLM calls, retrieval steps, embeddings, and tool calls
- **Span Metadata**: Records model name, token counts, latency, and custom attributes on every span
- **Custom Exporters**: Pluggable span exporters for cloud storage (S3, Azure Blob, GCS) and monitoring systems
- **Vector Store Tracing**: Captures retrieval spans from Chroma, Pinecone, Weaviate, and other vector stores used in RAG pipelines

## Installation and Setup

Install Monocle:

```bash
pip install monocle-observability
```

For additional cloud exporters:

```bash
# AWS S3 exporter
pip install monocle-observability[aws]

# Azure Blob Storage exporter
pip install monocle-observability[azure]

# Google Cloud Storage exporter
pip install monocle-observability[gcp]
```

No external API key is required to use Monocle with a local or self-hosted OTLP backend.

## Using This Skill

### Basic Setup with Console Exporter

Set up Monocle to trace a GenAI workflow and print spans to the console for local development:

```python
from monocle_apptrace.instrumentation.common.instrumentor import setup_monocle_telemetry
from opentelemetry.sdk.trace.export import ConsoleSpanExporter, BatchSpanProcessor

# Configure Monocle with a console exporter for development
setup_monocle_telemetry(
    workflow_name="research-pipeline",
    span_processors=[BatchSpanProcessor(ConsoleSpanExporter())]
)
```

### Instrumenting a LangChain Research Pipeline

Monocle automatically instruments LangChain when imported after setup:

```python
from monocle_apptrace.instrumentation.common.instrumentor import setup_monocle_telemetry
from opentelemetry.sdk.trace.export import ConsoleSpanExporter, BatchSpanProcessor
from langchain.chains import RetrievalQA
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
from langchain_community.vectorstores import Chroma

# Initialize Monocle — must be called before importing LangChain chains
setup_monocle_telemetry(
    workflow_name="pubmed-literature-qa",
    span_processors=[BatchSpanProcessor(ConsoleSpanExporter())]
)

# Build a RAG chain for scientific literature Q&A
embeddings = OpenAIEmbeddings()
vectorstore = Chroma(
    collection_name="pubmed_abstracts",
    embedding_function=embeddings,
    persist_directory="./pubmed_chroma_db"
)

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=vectorstore.as_retriever(search_kwargs={"k": 5}),
    return_source_documents=True
)

# Monocle automatically traces: retrieval span + LLM span
result = qa_chain.invoke("What are the latest findings on RNA methylation in cancer?")
print(result["result"])
```

### Exporting Traces to a Self-Hosted Jaeger Backend

Route traces to a locally hosted Jaeger instance for persistent storage and visualization:

```python
from monocle_apptrace.instrumentation.common.instrumentor import setup_monocle_telemetry
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace.export import BatchSpanProcessor

# Jaeger running on the same HPC cluster node
jaeger_exporter = OTLPSpanExporter(endpoint="http://localhost:4317", insecure=True)

setup_monocle_telemetry(
    workflow_name="clinical-nlp-pipeline",
    span_processors=[BatchSpanProcessor(jaeger_exporter)]
)

# All subsequent LangChain or OpenAI calls are exported to Jaeger
run_clinical_entity_extraction(clinical_notes)
```

### Tracing a Multi-Step Scientific Analysis Pipeline

Instrument a multi-stage pipeline that combines retrieval, reasoning, and structured output:

```python
import os
from monocle_apptrace.instrumentation.common.instrumentor import setup_monocle_telemetry
from opentelemetry.sdk.trace.export import ConsoleSpanExporter, BatchSpanProcessor
from opentelemetry import trace
from langchain_openai import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from langchain_core.output_parsers import JsonOutputParser

setup_monocle_telemetry(
    workflow_name="genomic-variant-interpretation",
    span_processors=[BatchSpanProcessor(ConsoleSpanExporter())]
)

tracer = trace.get_tracer("genomic-pipeline")
llm = ChatOpenAI(model="gpt-4o", temperature=0)

def interpret_variant(gene: str, variant: str, clinical_significance: str) -> dict:
    """Interpret a genomic variant using an LLM with Monocle tracing."""
    with tracer.start_as_current_span("variant-interpretation") as span:
        span.set_attribute("gene", gene)
        span.set_attribute("variant", variant)

        prompt = ChatPromptTemplate.from_messages([
            ("system", "You are a clinical geneticist. Interpret genomic variants clearly."),
            ("user", "Gene: {gene}\nVariant: {variant}\nClinVar: {significance}\nProvide: mechanism, clinical impact, recommendations.")
        ])

        parser = JsonOutputParser()
        chain = prompt | llm | parser

        result = chain.invoke({
            "gene": gene,
            "variant": variant,
            "significance": clinical_significance
        })

        span.set_attribute("interpretation_keys", str(list(result.keys())))
        return result

interpretation = interpret_variant("BRCA1", "c.5266dupC", "Pathogenic")
print(interpretation)
```

### Custom Span Attributes for Scientific Metadata

Add domain-specific metadata to spans for filtering and analysis:

```python
from monocle_apptrace.instrumentation.common.instrumentor import setup_monocle_telemetry
from opentelemetry import trace
from opentelemetry.sdk.trace.export import ConsoleSpanExporter, BatchSpanProcessor

setup_monocle_telemetry(
    workflow_name="protein-analysis-pipeline",
    span_processors=[BatchSpanProcessor(ConsoleSpanExporter())]
)

tracer = trace.get_tracer("protein-pipeline")

def analyze_protein_family(family_name: str, sequences: list[str]) -> list[dict]:
    """Analyze a protein family with custom span attributes."""
    with tracer.start_as_current_span("protein-family-analysis") as span:
        span.set_attribute("protein_family", family_name)
        span.set_attribute("sequence_count", len(sequences))
        span.set_attribute("experiment_id", "EXP-2026-003")

        results = []
        for i, seq in enumerate(sequences):
            with tracer.start_as_current_span(f"analyze-sequence-{i}") as seq_span:
                seq_span.set_attribute("sequence_length", len(seq))
                result = run_domain_prediction(seq)
                seq_span.set_attribute("domains_found", len(result.get("domains", [])))
                results.append(result)

        span.set_attribute("successful_analyses", len(results))
        return results
```

### Google ADK Integration

Instrument Google ADK agents with Monocle for infrastructure-agnostic observability:

```python
import os
from monocle_apptrace.instrumentation.common.instrumentor import setup_monocle_telemetry
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from google.adk.agents import Agent
from google.adk.tools import tool
from opentelemetry import trace

# Export to institutional OTLP collector — no data leaves the institution
otlp_exporter = OTLPSpanExporter(
    endpoint=os.environ.get("OTLP_ENDPOINT", "http://internal-collector:4317"),
    insecure=True
)

setup_monocle_telemetry(
    workflow_name="adk-scientific-research-agent",
    span_processors=[BatchSpanProcessor(otlp_exporter)]
)

tracer = trace.get_tracer("adk-agent")

@tool
def query_genomics_database(gene_symbol: str, organism: str = "human") -> dict:
    """Query an internal genomics database for gene information."""
    with tracer.start_as_current_span("genomics-db-query") as span:
        span.set_attribute("gene_symbol", gene_symbol)
        span.set_attribute("organism", organism)
        result = internal_genomics_db.query(gene_symbol, organism)
        span.set_attribute("records_returned", len(result.get("records", [])))
        return result

@tool
def run_pathway_enrichment(gene_list: list[str], database: str = "KEGG") -> dict:
    """Run pathway enrichment analysis on a gene list."""
    with tracer.start_as_current_span("pathway-enrichment") as span:
        span.set_attribute("gene_count", len(gene_list))
        span.set_attribute("database", database)
        result = pathway_analysis.enrich(gene_list, database)
        span.set_attribute("significant_pathways", result.get("significant_count", 0))
        return result

genomics_agent = Agent(
    model="gemini-2.0-flash",
    name="genomics_analyst",
    instruction="Analyze genomic data and identify enriched biological pathways.",
    tools=[query_genomics_database, run_pathway_enrichment]
)

# All ADK agent interactions and tool calls are traced via Monocle to internal OTLP
response = genomics_agent.run("Find pathways enriched in genes upregulated in BRCA1-mutant tumors.")
```

## Additional Resources

- Monocle GitHub: https://github.com/monocle2ai/monocle
- Monocle documentation: https://monocle2ai.github.io/monocle
- OpenTelemetry Python SDK: https://opentelemetry-python.readthedocs.io
- OTLP exporter reference: https://opentelemetry-python.readthedocs.io/en/latest/exporter/otlp/otlp.html
