# NotebookLM Python API Reference

Package: `notebooklm-py` v0.3.4 — `pip install notebooklm-py`

## Authentication

```python
from notebooklm import NotebookLMClient

# Method 1: From saved browser session (after `notebooklm login`)
async with NotebookLMClient.from_storage() as client:
    ...

# Method 2: Explicit storage path
async with NotebookLMClient.from_storage(
    storage_path="~/.notebooklm/storage_state.json"
) as client:
    ...
```

## Notebooks

```python
# List all notebooks
notebooks = await client.notebooks.list()
# Returns: List[Notebook] with .id, .title, .created_at

# Create notebook
nb = await client.notebooks.create(title="My Notebook")

# Delete notebook
await client.notebooks.delete(notebook_id=nb.id)

# Rename notebook
await client.notebooks.rename(notebook_id=nb.id, title="New Name")

# Get notebook summary
summary = await client.notebooks.summary(notebook_id=nb.id)

# Partial ID matching works in CLI (first 6+ chars)
```

## Sources

```python
# Add URL source
source = await client.sources.add_url(
    notebook_id=nb.id,
    url="https://pubmed.ncbi.nlm.nih.gov/19706460/"
)

# Add text content
source = await client.sources.add_text(
    notebook_id=nb.id,
    content="<html>...</html>",  # or plain text
    title="Report Name"
)

# Add local file (PDF, txt, docx, html)
source = await client.sources.add_file(
    notebook_id=nb.id,
    file_path="/path/to/report.pdf"
)

# Add YouTube video
source = await client.sources.add_url(
    notebook_id=nb.id,
    url="https://www.youtube.com/watch?v=..."
)

# Wait for source to be processed/indexed
source = await client.sources.wait(
    notebook_id=nb.id,
    source_id=source.id,
    timeout=120  # seconds
)

# List sources
sources = await client.sources.list(notebook_id=nb.id)

# Get full text of source
text = await client.sources.fulltext(
    notebook_id=nb.id,
    source_id=source.id
)

# Delete source
await client.sources.delete(
    notebook_id=nb.id,
    source_id=source.id
)
```

## Artifact Generation

Available artifact types:
- `"mind-map"` — interactive mind map
- `"infographic"` — visual infographic
- `"slide-deck"` — presentation slides
- `"audio"` — podcast-style audio (MP3)
- `"video"` — video overview
- `"quiz"` — quiz questions
- `"flashcards"` — study flashcards
- `"data-table"` — structured data table
- `"report"` — briefing doc / study guide / blog post / custom

```python
# Generate artifact
artifact = await client.artifacts.generate(
    notebook_id=nb.id,
    artifact_type="mind-map",  # see types above
    instructions="Optional custom instructions"
)

# Wait for generation to complete
artifact = await client.artifacts.wait(
    notebook_id=nb.id,
    artifact_id=artifact.id,
    timeout=300  # 5 minutes for audio
)

# List artifacts
artifacts = await client.artifacts.list(notebook_id=nb.id)

# Get artifact details
artifact = await client.artifacts.get(
    notebook_id=nb.id,
    artifact_id=artifact.id
)

# Export to Google Docs/Slides
export = await client.artifacts.export(
    notebook_id=nb.id,
    artifact_id=artifact.id,
    export_type="google-docs"  # or "google-sheets"
)

# Get artifact suggestions (related content)
suggestions = await client.artifacts.suggestions(
    notebook_id=nb.id,
    artifact_id=artifact.id
)
```

## Chat / Ask

```python
# Ask a question
response = await client.chat.ask(
    notebook_id=nb.id,
    question="What is the most important genetic finding?"
)
# Returns: str with answer

# Get conversation history
history = await client.chat.history(notebook_id=nb.id)

# Configure chat persona
await client.chat.configure(
    notebook_id=nb.id,
    persona="expert",  # or "tutor", "critic"
    response_style="detailed"  # or "concise"
)
```

## Notes

```python
# Create user note
note = await client.notes.create(
    notebook_id=nb.id,
    content="Key finding: FOXO3 GG genotype confirmed"
)

# List notes
notes = await client.notes.list(notebook_id=nb.id)
```

## Settings

```python
# Set output language
await client.settings.set_language(language="ro")  # Romanian

# Get current language
lang = await client.settings.get_language()
```

## CLI Reference

```bash
# Authentication
notebooklm login              # Browser-based login
notebooklm auth status        # Check auth state
notebooklm auth clear         # Clear saved session

# Notebooks
notebooklm list               # List all notebooks
notebooklm create "Name"      # Create notebook
notebooklm delete <id>        # Delete notebook
notebooklm use <id>           # Set active notebook
notebooklm summary            # Get notebook summary

# Sources
notebooklm source add <url|file>  # Add source
notebooklm source list            # List sources
notebooklm source fulltext <id>   # Get full text
notebooklm source delete <id>     # Delete source
notebooklm source wait <id>       # Wait for indexing

# Artifact Generation
notebooklm generate mind-map [--prompt "..."]
notebooklm generate infographic [--prompt "..."]
notebooklm generate slide-deck [--prompt "..."]
notebooklm generate audio [--prompt "..."]
notebooklm generate video [--prompt "..."]
notebooklm generate quiz
notebooklm generate flashcards
notebooklm generate data-table
notebooklm generate report [--type briefing|study-guide|blog|custom]

# Download
notebooklm download mind-map --output /path/
notebooklm download infographic --output /path/
notebooklm download audio --output /path/

# Artifacts
notebooklm artifact list          # List all artifacts
notebooklm artifact get <id>      # Get artifact details
notebooklm artifact export <id>   # Export to Google Docs/Slides
notebooklm artifact suggestions <id>

# Chat
notebooklm ask "question"         # Ask about notebook content
notebooklm ask history            # View conversation history

# Research
notebooklm research web "topic"   # Web research session
notebooklm research drive "topic" # Google Drive research

# Sharing
notebooklm share list             # List sharing settings
```

## Error Handling

```python
from notebooklm import NotebookLMClient
from notebooklm.exceptions import (
    AuthenticationError,
    NotebookNotFoundError,
    SourceProcessingError,
    ArtifactGenerationError
)

async with NotebookLMClient.from_storage() as client:
    try:
        notebook = await client.notebooks.create(title="Test")
    except AuthenticationError:
        print("Run: notebooklm login")
    except SourceProcessingError as e:
        print(f"Source failed: {e}")
```

## Common Patterns for ABLE GENETICS

```python
# Pattern: Full report → diagrams pipeline
async def process_able_report(report_html_path: str, client_name: str):
    async with NotebookLMClient.from_storage() as nlm:
        # Setup
        nb = await nlm.notebooks.create(title=f"ABLE — {client_name}")
        src = await nlm.sources.add_file(nb.id, report_html_path)
        await nlm.sources.wait(nb.id, src.id)

        # Generate in parallel (start all, then wait)
        tasks = [
            nlm.artifacts.generate(nb.id, "mind-map"),
            nlm.artifacts.generate(nb.id, "infographic"),
            nlm.artifacts.generate(nb.id, "audio"),
        ]
        import asyncio
        artifacts = await asyncio.gather(*tasks)

        # Wait for all to complete
        for artifact in artifacts:
            await nlm.artifacts.wait(nb.id, artifact.id)

        return {"notebook_id": nb.id, "artifacts": [a.id for a in artifacts]}
```
