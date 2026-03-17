---
name: mlflow
description: MLflow is an open source ML lifecycle platform for experiment tracking, model registry, and artifact management. Use this skill when you need to track scientific ML experiments, compare model runs, version trained models, or build reproducible ML pipelines in research environments.
license: Apache-2.0
metadata:
    skill-author: K-Dense Inc.
---

# MLflow

## Overview

MLflow is an open source platform that manages the full machine learning lifecycle, from experiment tracking through model deployment. Developed by Databricks and widely adopted in academia and industry, MLflow provides four core components: Tracking (logging parameters, metrics, and artifacts), Projects (packaging ML code for reproducibility), Models (standardized model format with deployment utilities), and Model Registry (versioned model repository with stage management).

For scientific research, MLflow addresses the challenge of experiment reproducibility by creating a structured record of every training run: the hyperparameters used, the metrics achieved, the datasets consumed, and the model artifacts produced. MLflow integrates with scikit-learn, PyTorch, TensorFlow, XGBoost, Hugging Face, LightGBM, and the Google Agent Development Kit (ADK).

## When to Use This Skill

Use MLflow when you need to:

- Track and compare dozens or hundreds of model training runs across different hyperparameter configurations
- Log scientific datasets, evaluation results, and figures as versioned artifacts
- Register trained models and promote them through development, staging, and production stages
- Package ML experiments as reproducible projects with pinned dependencies
- Compare metrics across experiment runs to select the best model for a research task
- Build automated ML pipelines where agents select and evaluate models based on tracked metrics
- Share experiment results with collaborators through a centralized MLflow tracking server

## Core Capabilities

- **Experiment Tracking**: Log parameters, metrics (including step-by-step curves), tags, and file artifacts to named experiments
- **Autologging**: One-line automatic capture of all parameters and metrics for supported frameworks
- **Model Registry**: Versioned model storage with stage transitions (None, Staging, Production, Archived)
- **Artifact Logging**: Store any file — plots, confusion matrices, datasets, model checkpoints — alongside run metadata
- **Comparison UI**: Browser-based interface to filter, sort, and visualize runs across experiments
- **MLflow Projects**: Reproducible execution environment defined by MLproject files
- **Plugins**: Extensible with custom logging backends, model flavors, and deployment targets

## Installation and Setup

Install MLflow:

```bash
pip install mlflow
```

For scikit-learn, PyTorch, or XGBoost autologging:

```bash
pip install mlflow scikit-learn torch xgboost
```

Start a local MLflow tracking server (optional; MLflow also logs to the local filesystem by default):

```bash
mlflow server --host 127.0.0.1 --port 5000
```

Point your code at the tracking server:

```bash
export MLFLOW_TRACKING_URI="http://127.0.0.1:5000"
```

## Using This Skill

### Basic Experiment Tracking

Log parameters, metrics, and a model artifact in a single training run:

```python
import mlflow
import mlflow.sklearn
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score, roc_auc_score

mlflow.set_experiment("protein-secondary-structure-prediction")

with mlflow.start_run(run_name="rf-baseline"):
    # Log hyperparameters
    mlflow.log_param("n_estimators", 200)
    mlflow.log_param("max_depth", 12)
    mlflow.log_param("feature_set", "physicochemical-v2")

    # Train model
    model = RandomForestClassifier(n_estimators=200, max_depth=12, random_state=42)
    model.fit(X_train, y_train)
    y_pred = model.predict(X_test)

    # Log evaluation metrics
    mlflow.log_metric("accuracy", accuracy_score(y_test, y_pred))
    mlflow.log_metric("roc_auc", roc_auc_score(y_test, model.predict_proba(X_test), multi_class="ovr"))

    # Log trained model as artifact
    mlflow.sklearn.log_model(model, "random-forest-model")
    print(f"Run ID: {mlflow.active_run().info.run_id}")
```

### Autologging for Rapid Iteration

Enable autologging to capture all framework-specific parameters and metrics automatically:

```python
import mlflow
import mlflow.sklearn
from sklearn.svm import SVC

mlflow.set_experiment("drug-toxicity-prediction")
mlflow.sklearn.autolog()  # Captures all SVC params and CV metrics automatically

with mlflow.start_run(run_name="svm-rbf"):
    svm = SVC(kernel="rbf", C=1.0, probability=True)
    svm.fit(X_train, y_train)
    # Parameters and cross-validation metrics logged automatically
```

### Logging Scientific Artifacts

Store figures, datasets, and reports alongside model metrics:

```python
import mlflow
import matplotlib.pyplot as plt
import pandas as pd
import os

mlflow.set_experiment("cell-segmentation")

with mlflow.start_run(run_name="unet-v3"):
    mlflow.log_params({"encoder": "resnet50", "loss": "dice", "batch_size": 16})
    mlflow.log_metrics({"val_dice": 0.91, "val_iou": 0.87})

    # Log evaluation figure
    fig, ax = plt.subplots()
    ax.plot(training_losses, label="Train Loss")
    ax.plot(val_losses, label="Val Loss")
    ax.set_title("Training Curve")
    ax.legend()
    mlflow.log_figure(fig, "training_curve.png")
    plt.close(fig)

    # Log evaluation dataset as artifact
    eval_results = pd.DataFrame({"image_id": image_ids, "dice": dice_scores})
    eval_results.to_csv("/tmp/eval_results.csv", index=False)
    mlflow.log_artifact("/tmp/eval_results.csv", artifact_path="evaluation")

    # Log PyTorch model
    mlflow.pytorch.log_model(unet_model, "unet-segmentation-model")
```

### Comparing Experiment Runs Programmatically

Query logged runs to identify the best model for a given metric:

```python
import mlflow

client = mlflow.tracking.MlflowClient()
experiment = client.get_experiment_by_name("drug-toxicity-prediction")

# Retrieve all runs sorted by validation AUC
runs = client.search_runs(
    experiment_ids=[experiment.experiment_id],
    filter_string="metrics.val_auc > 0.80",
    order_by=["metrics.val_auc DESC"]
)

best_run = runs[0]
print(f"Best run ID: {best_run.info.run_id}")
print(f"Best val AUC: {best_run.data.metrics['val_auc']:.4f}")
print(f"Hyperparameters: {best_run.data.params}")
```

### Model Registry and Versioning

Register and promote models through lifecycle stages:

```python
import mlflow

# Register model from a completed run
model_uri = f"runs:/{best_run.info.run_id}/unet-segmentation-model"
registered = mlflow.register_model(model_uri, "CellSegmentationUNet")

client = mlflow.tracking.MlflowClient()

# Transition to staging for validation
client.transition_model_version_stage(
    name="CellSegmentationUNet",
    version=registered.version,
    stage="Staging"
)

# Load the staged model for evaluation
staged_model = mlflow.pytorch.load_model(
    f"models:/CellSegmentationUNet/Staging"
)
```

### Google ADK Integration

Use MLflow within a Google ADK agent to select and serve the best registered model dynamically:

```python
import mlflow
from google.adk.agents import Agent
from google.adk.tools import tool

mlflow.set_tracking_uri(os.environ.get("MLFLOW_TRACKING_URI", "http://127.0.0.1:5000"))
client = mlflow.tracking.MlflowClient()

@tool
def get_best_model_run(experiment_name: str, metric: str) -> dict:
    """Retrieve the best MLflow run for a given experiment and metric."""
    experiment = client.get_experiment_by_name(experiment_name)
    if not experiment:
        return {"error": f"Experiment '{experiment_name}' not found."}
    runs = client.search_runs(
        experiment_ids=[experiment.experiment_id],
        order_by=[f"metrics.{metric} DESC"]
    )
    if not runs:
        return {"error": "No runs found."}
    best = runs[0]
    return {
        "run_id": best.info.run_id,
        "metric_value": best.data.metrics.get(metric),
        "params": best.data.params
    }

@tool
def log_prediction_quality(experiment_name: str, run_name: str, metric_name: str, value: float) -> str:
    """Log an evaluation metric to an MLflow experiment from an ADK agent."""
    mlflow.set_experiment(experiment_name)
    with mlflow.start_run(run_name=run_name):
        mlflow.log_metric(metric_name, value)
    return f"Logged {metric_name}={value} to {experiment_name}/{run_name}"

ml_orchestrator = Agent(
    model="gemini-2.0-flash",
    name="ml_orchestrator",
    instruction="You manage ML experiments. Find the best models and log evaluation results.",
    tools=[get_best_model_run, log_prediction_quality]
)
```

## Additional Resources

- MLflow documentation: https://mlflow.org/docs/latest/index.html
- MLflow GitHub: https://github.com/mlflow/mlflow
- Model Registry guide: https://mlflow.org/docs/latest/model-registry.html
- Autologging reference: https://mlflow.org/docs/latest/tracking/autolog.html
