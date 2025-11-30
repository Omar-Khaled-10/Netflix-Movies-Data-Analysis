# 🤖 Multi-Agent AI Code Review Platform

An advanced, desktop-style application for comprehensive code analysis. This platform orchestrates a team of specialized **Large Language Model (LLM) agents** to provide deep, structured, and actionable feedback on code quality, security, performance, and architecture.

Built with **Python (Flask) backend** for orchestration and a modern **Vanilla JavaScript/Tailwind CSS frontend** for a responsive, VS Code-like user experience.

## ✨ Features and Capabilities

This platform employs a robust system of nine specialized LLM agents, each strictly constrained by Pydantic schemas to deliver reliable, structured JSON reports.

### The Specialized Agent Suite

| Category | Agent Name | Core Focus Area | Output Schema |
| :--- | :--- | :--- | :--- |
| **Foundation** | **Summarizer** | High-level, neutral explanation of the code's purpose and functionality. | `SummaryResponse` |
| **Static Analysis** | **Code Smell** | Identifies common anti-patterns like Long Methods, Magic Numbers, and Poor Naming. | `CodeSmellReport` |
| | **Complexity** | Measures code difficulty using metrics like Cyclomatic Complexity and deep nesting. | `ComplexityReport` |
| | **Dependency** | Flags unused imports, deprecated packages, and unpinned/outdated dependencies. | `DependencyReport` |
| **Quality & Design**| **SOLID** | Analyzes adherence to the fundamental **SOLID** object-oriented principles. | `SolidReport` |
| | **Architecture**| Focuses on structural issues like Tight Coupling, Low Cohesion, and Layer Violations. | `ArchitectureReport` |
| | **Refactor** | Provides practical, human-friendly, and actionable suggestions for code improvement. | `RefactorReport` |
| **Risk & Performance**| **Security** | Detects critical vulnerabilities: Injection, Hardcoded Secrets, Path Traversal, and weak cryptography. | `SecurityReport` |
| | **Performance**| Pinpoints runtime bottlenecks: O(n²) behavior, inefficient data structures, and caching issues. | `PerformanceReport` |

-----

## 📐 Architecture

The project follows a clean, decoupled structure, separating the core analysis logic (Python) from the user interface (Frontend).

[Image of a Multi-Agent System Architecture Diagram]

### Backend: Python (Flask & Gemini)

The heart of the application, responsible for reading code, managing jobs, and interfacing with the LLM.

  * **`app.py`:** The main server entry point. Handles routing, session state (`session_state.json`), configuration loading (`config.yaml`), and initializes the LLM provider (`GoogleProvider`).
  * **`orchestration.py`:** The **Analysis Orchestrator**. This class is responsible for:
      * Starting and managing batch analysis jobs across multiple files and agents.
      * Implementing job features like **real-time logging**, **progress tracking**, and **cancellation**.
      * Applying **retry logic** (`_run_with_retry`) for reliable LLM calls.
  * **Agents (`*agents.py`):** Each agent inherits from `base_agent.BaseAgent` and defines a unique `system_prompt` to constrain the LLM's expertise (e.g., "You are a senior performance engineer...").
  * **Schemas (`*schemas.py`):** Uses **Pydantic** models to strictly enforce the output structure of every agent's report. All reports inherit from `BaseLLMResponse`, which requires a highly detailed, Markdown-formatted `MarkdownDescription` field.
  * **`file_loader.py`:** Provides secure file handling, including size checks and path validation, to prevent security risks.

### Frontend: Web Interface (SPA)

A single-page application designed for a powerful, desktop-like development environment experience.

  * **Core UI:** Uses **Tailwind CSS** for styling and a custom **VS Code-like dark theme** (`styles.css`).
  * **`index.html`:** The main workspace. Features a **File Tree** sidebar, a resizable **Code Editor** panel, and a tabbed **Analysis Results** view. Includes a non-blocking modal for running and tracking batch jobs.
  * **`dashboard.html`:** A dedicated metrics overview page, providing a high-level summary of analysis results, and likely featuring data visualization (e.g., Test Opportunity Candidates).
  * **`settings.html`:** A configuration panel for managing LLM connections (e.g., API keys, model names for **Cloud LLM** or **Local LLM** endpoints).
  * **`store.js`:** Manages global state variables (`projectRoot`, `currentFile`, `files`, `agentResults`, `AGENT_KEYS`, and progress flags).
  * **`main.js` & `features.js`:** The controller logic for file navigation, code loading, starting/polling analysis jobs, and session persistence.
  * **`view.js`:** Handles all DOM manipulation, including the custom **markdown rendering** for the agent reports and UI synchronization (e.g., scroll sync for line numbers and code).

-----

## 🚀 Getting Started

Follow these steps to set up and run the Code Analysis Studio locally.

### Prerequisites

  * Python 3.9+
  * A Google API Key (or other LLM provider key, configured in `settings.html`).

### 1\. Setup

Clone the repository and install dependencies.

```bash
# Clone the repository
git clone <your-repository-url>
cd multi-agent-ai-code-review

# Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate 

# Install dependencies (Assuming a requirements.txt is generated from your project)
pip install -r requirements.txt 
```

### 2\. Configuration

Set your API key and review settings:

1.  **API Key:** Create a file named `.env` in the project root and add your Google API Key:

    **.env**

    ```
    GOOGLE_API_KEY="YOUR_GEMINI_API_KEY_HERE"
    ```

2.  **Fine-tuning:** Review and adjust core parameters in `config.yaml`, such as `allowed_extensions`, `max_file_size_kb`, LLM `timeout`, and analysis `retry` settings.

    **config.yaml snippet:**

    ```yaml
    allowed_extensions:
      - .py
      - .js
      - .ts
      # ... other extensions
    max_file_size_kb: 300
    retry:
      attempts: 3
      # ...
    ```

### 3\. Run the Application

Start the Flask development server:

```bash
python app.py
```

### 4\. Usage

1.  Open your browser and navigate to the local address (e.g., `http://127.0.0.1:5000`).
2.  Use the interface to select a **Project Root Path** (or use the directory picker on the server machine).
3.  Select files from the **File Tree**.
4.  Run a **Batch Analysis** to execute all 9 agents on the selected files concurrently.
5.  View the results in the tabbed panel, featuring detailed, structured reports rendered in Markdown. Use the **Dashboard** for a high-level overview of project health.

-----

## 🛠️ Key Technologies

  * **Backend:** Python, Flask, `pydantic-ai` (for structured LLM output), `OmegaConf` (for config management).
  * **LLM Provider:** Google Gemini API (Configurable for other models).
  * **Frontend:** Vanilla JavaScript, HTML5, **Tailwind CSS**, FontAwesome.
  * **Styling:** Custom VS Code-inspired dark mode theme (`styles.css`).
  * **Job Management:** Multithreading (`threading` module) for concurrent, non-blocking batch analysis.
