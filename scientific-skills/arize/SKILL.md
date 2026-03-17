---
name: arize
description: Arize AI is an ML observability and model monitoring platform for detecting data drift, tracking model performance, and debugging production ML systems. Use this skill when you need to monitor ML models in scientific research pipelines, identify distribution shifts in biological or clinical data, or diagnose prediction quality degradation over time.
license: BSD-3-Clause
metadata:
    skill-author: K-Dense Inc.
---

# Arize AI

## Overview

Arize AI is a production ML observability platform that helps data scientists and ML engineers monitor model health, detect data and concept drift, and investigate prediction quality in real time. For scientific research, Arize provides the infrastructure to track model behavior across experimental conditions, flag unexpected distributional changes in genomic or clinical datasets, and maintain a full audit trail of model predictions and ground truth labels.

Arize supports regression, classification, NLP, computer vision, and ranking models. It integrates with major ML frameworks including scikit-learn, PyTorch, TensorFlow, XGBoost, Hugging Face, and the Google Agent Development Kit (ADK), and works alongside tools like MLflow and Weights & Biases in existing research MLOps stacks.

## When to Use This Skill

Use Arize when you need to:

- Monitor a trained ML model serving predictions in an ongoing scientific study or clinical trial
- Detect when input feature distributions shift between training and inference (covariate shift)
- Track prediction accuracy over time as new ground truth labels become available
- Debug unexpected model behavior on specific patient cohorts, experimental conditions, or data subgroups
- Maintain regulatory-compliant audit logs of model predictions for clinical or pharmaceutical applications
- Compare model performance across multiple versions or retraining cycles

## Core Capabilities

- **Data Drift Detection**: Automatically computes statistical tests (PSI, KL divergence, Wasserstein) to detect feature and prediction distribution shifts
- **Performance Monitoring**: Tracks accuracy, AUC, RMSE, and custom metrics as ground truth labels arrive
- **Embedding Analysis**: Visualizes high-dimensional embeddings (e.g., from protein language models or medical imaging CNNs) to detect cluster shifts
- **Slice Analysis**: Segments predictions by cohort, time window, or feature value to surface subgroup performance issues
- **Alerting**: Configurable alerts for drift thresholds, performance degradation, and volume anomalies
- **Explainability**: Logs SHAP feature importance values alongside predictions for interpretability audits
- **Model Comparison**: Side-by-side comparison of model versions to guide retraining decisions

## Installation and Setup

Install the Arize Python SDK:

```bash
pip install arize
```

For embedding and NLP support:

```bash
pip install arize[NLP,AutoEmbeddings]
```

Retrieve your Space Key and API Key from the Arize dashboard at https://app.arize.com. Store credentials securely:

```bash
export ARIZE_SPACE_KEY="your_space_key"
export ARIZE_API_KEY="your_api_key"
```

## Using This Skill

### Initializing the Arize Client

```python
import os
from arize.api import Client
from arize.utils.types import ModelTypes, Environments

arize_client = Client(
    space_key=os.environ.get("ARIZE_SPACE_KEY"),
    api_key=os.environ.get("ARIZE_API_KEY")
)
```

### Logging Classification Predictions for Clinical ML

Log predictions from a disease classification model alongside ground truth labels and patient metadata:

```python
import pandas as pd
from arize.api import Client
from arize.utils.types import ModelTypes, Environments, Schema

arize_client = Client(
    space_key=os.environ.get("ARIZE_SPACE_KEY"),
    api_key=os.environ.get("ARIZE_API_KEY")
)

# Example: logging predictions from a cancer subtype classifier
predictions_df = pd.DataFrame({
    "prediction_id": ["patient_001", "patient_002", "patient_003"],
    "predicted_label": ["adenocarcinoma", "squamous_cell", "adenocarcinoma"],
    "prediction_score": [0.87, 0.73, 0.91],
    "actual_label": ["adenocarcinoma", "adenocarcinoma", "adenocarcinoma"],
    "age": [54, 67, 45],
    "smoking_history": [1, 1, 0],
    "tumor_size_mm": [22.3, 18.7, 31.1]
})

schema = Schema(
    prediction_id_column_name="prediction_id",
    prediction_label_column_name="predicted_label",
    prediction_score_column_name="prediction_score",
    actual_label_column_name="actual_label",
    feature_column_names=["age", "smoking_history", "tumor_size_mm"]
)

response = arize_client.log(
    dataframe=predictions_df,
    schema=schema,
    model_id="lung-cancer-classifier",
    model_version="v2.3",
    model_type=ModelTypes.SCORE_CATEGORICAL,
    environment=Environments.PRODUCTION
)

if response.status_code == 200:
    print("Predictions logged to Arize successfully.")
```

### Monitoring Regression Models in Genomics

Track a continuous-output model, such as gene expression prediction from DNA sequence:

```python
from arize.utils.types import ModelTypes, Environments, Schema
import pandas as pd

expression_df = pd.DataFrame({
    "record_id": [f"seq_{i}" for i in range(100)],
    "predicted_expression": predicted_tpm_values,
    "actual_expression": measured_tpm_values,
    "gc_content": gc_content_values,
    "sequence_length": seq_lengths,
    "promoter_strength": promoter_scores
})

schema = Schema(
    prediction_id_column_name="record_id",
    prediction_score_column_name="predicted_expression",
    actual_score_column_name="actual_expression",
    feature_column_names=["gc_content", "sequence_length", "promoter_strength"]
)

arize_client.log(
    dataframe=expression_df,
    schema=schema,
    model_id="gene-expression-predictor",
    model_version="v1.0",
    model_type=ModelTypes.REGRESSION,
    environment=Environments.PRODUCTION
)
```

### Detecting Data Drift in Longitudinal Studies

Log training data as a baseline reference so Arize can detect distribution shifts in production data from follow-up cohorts:

```python
from arize.utils.types import Environments

# Log training baseline
arize_client.log(
    dataframe=training_df,
    schema=schema,
    model_id="biomarker-predictor",
    model_version="v1.0",
    model_type=ModelTypes.REGRESSION,
    environment=Environments.TRAINING
)

# Log new cohort predictions — Arize computes drift vs. training baseline
arize_client.log(
    dataframe=followup_cohort_df,
    schema=schema,
    model_id="biomarker-predictor",
    model_version="v1.0",
    model_type=ModelTypes.REGRESSION,
    environment=Environments.PRODUCTION
)
```

### Logging Protein Embeddings for Similarity Monitoring

Monitor embedding space drift for protein language model representations:

```python
import numpy as np
from arize.utils.types import ModelTypes, Environments, Schema, EmbeddingColumnNames

embeddings_df = pd.DataFrame({
    "protein_id": protein_ids,
    "predicted_function": predicted_functions,
    "actual_function": actual_functions,
    "embedding_vector": [emb.tolist() for emb in protein_embeddings],
    "organism": organisms,
    "sequence_length": seq_lengths
})

schema = Schema(
    prediction_id_column_name="protein_id",
    prediction_label_column_name="predicted_function",
    actual_label_column_name="actual_function",
    embedding_feature_column_names={
        "protein_embedding": EmbeddingColumnNames(
            vector_column_name="embedding_vector"
        )
    },
    tag_column_names=["organism", "sequence_length"]
)

arize_client.log(
    dataframe=embeddings_df,
    schema=schema,
    model_id="protein-function-classifier",
    model_version="esm2-v1",
    model_type=ModelTypes.SCORE_CATEGORICAL,
    environment=Environments.PRODUCTION
)
```

### Google ADK Integration

Use Arize to monitor ML models invoked by Google ADK agents in research automation pipelines:

```python
import os
from arize.api import Client
from arize.utils.types import ModelTypes, Environments, Schema
from google.adk.agents import Agent
from google.adk.tools import tool

arize_client = Client(
    space_key=os.environ.get("ARIZE_SPACE_KEY"),
    api_key=os.environ.get("ARIZE_API_KEY")
)

@tool
def classify_cell_type(features: dict) -> str:
    """Classify cell type from scRNA-seq features and log prediction to Arize."""
    prediction = cell_type_model.predict([list(features.values())])[0]
    score = cell_type_model.predict_proba([list(features.values())]).max()

    # Log prediction to Arize for monitoring
    log_df = pd.DataFrame([{
        "prediction_id": features.get("cell_id", "unknown"),
        "predicted_label": prediction,
        "prediction_score": score,
        **features
    }])
    arize_client.log(
        dataframe=log_df,
        schema=Schema(
            prediction_id_column_name="prediction_id",
            prediction_label_column_name="predicted_label",
            prediction_score_column_name="prediction_score"
        ),
        model_id="scrna-cell-classifier",
        model_version="v3",
        model_type=ModelTypes.SCORE_CATEGORICAL,
        environment=Environments.PRODUCTION
    )
    return prediction

cell_analysis_agent = Agent(
    model="gemini-2.0-flash",
    name="cell_analysis_agent",
    instruction="Analyze single-cell RNA sequencing data and classify cell types.",
    tools=[classify_cell_type]
)
```

## Additional Resources

- Arize documentation: https://docs.arize.com
- Arize dashboard: https://app.arize.com
- GitHub repository: https://github.com/Arize-ai/arize-python-client
- Python SDK reference: https://docs.arize.com/arize/python-sdk/api-reference
