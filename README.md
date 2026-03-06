
# Adhoc: Auto-Documenting Codebase Changes

Adhoc is a command-line tool designed to automatically document changes in your codebase. By integrating with local language models, it generates detailed explanations of code modifications and compiles them into professional documentation formats such as LaTeX, Markdown, or Word. This tool streamlines the documentation process, making it effortless for developers to maintain up-to-date records of their code evolution.

[![PyPI Downloads](https://static.pepy.tech/badge/adhoc-python)](https://pepy.tech/projects/adhoc-python)


Watch the demo video to see Adhoc in action:

![Adhoc Demo](adhoc_demo.gif)

## Table of Contents
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
  - [Initialization](#initialization)
  - [Committing Changes](#committing-changes)
  - [Generating Documentation](#generating-documentation)
  - [Configuration](#configuration)
- [Requirements](#requirements)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgements](#acknowledgements)
Features
Automatic Documentation

Adhoc continuously analyzes modifications made to your codebase and converts those changes into structured documentation. Instead of developers manually describing what changed and why, Adhoc uses Large Language Models (LLMs) to infer the intent and significance of each modification. This enables the generation of human-readable explanations that remain closely aligned with the technical details of the underlying code.

This capability significantly reduces the documentation burden in fast-moving projects where frequent commits make manual documentation impractical. Adhoc extracts contextual information directly from diffs and file changes, ensuring that explanations are accurate and tied to the real evolution of the project.

Over time, this produces a chronological narrative of the system's development. Developers can review not only what changed, but also the reasoning and architectural implications behind each change. This makes Adhoc especially valuable for onboarding new contributors or reviewing historical decisions.

By automating documentation, Adhoc also encourages better development hygiene. Teams no longer need to choose between shipping features quickly and maintaining clear documentation; both occur simultaneously.

Multiple LLM Providers

Adhoc is designed to work with both local and remote LLM providers, offering flexibility depending on developer preferences and infrastructure constraints. By default, the tool integrates with Ollama, enabling local inference using models such as Llama, Mistral, or other supported models. This is useful for developers who prioritize privacy, offline capabilities, or reduced API costs.

For teams that require stronger reasoning capabilities or larger context windows, Adhoc can also connect to OpenAI-compatible APIs. This includes services like OpenAI itself, OpenRouter, LiteLLM, or other model gateways that follow the same API specification.

Because the provider abstraction is built into the configuration layer, switching models or providers requires minimal changes. Developers can transition between local experimentation and production-grade models simply by updating configuration parameters.

This flexibility ensures that Adhoc can adapt to a wide range of workflows, from individual developers experimenting locally to larger teams integrating it into CI/CD pipelines.

Multiple Output Formats

Adhoc supports generating documentation in multiple output formats, allowing teams to integrate documentation directly into their preferred workflow. Markdown output is ideal for projects that maintain documentation within their repositories, especially those using platforms like GitHub or GitLab.

For teams producing more formal technical reports, Adhoc can generate LaTeX documents that compile into professionally formatted PDFs. This is particularly useful for academic projects, research environments, or technical teams that require high-quality typeset documentation.

The tool also supports Word document generation using python-docx. This enables integration with business workflows where documentation is reviewed, edited, or distributed in standard office formats.

By supporting multiple formats, Adhoc ensures that generated documentation can easily move between developer-focused environments and formal reporting systems without requiring additional manual formatting.

Version Control Integration

Adhoc integrates naturally with version-controlled projects by monitoring changes between snapshots of the codebase. When a commit command is executed, the tool compares the current project state with the previously recorded snapshot and identifies all modifications.

These changes are then analyzed and interpreted by the configured language model, which generates structured explanations describing what was modified and why the change may matter. This produces documentation that evolves alongside the code.

Because Adhoc operates alongside version control systems rather than replacing them, developers can continue using Git or other tools exactly as they normally would. Adhoc simply augments the workflow by capturing additional explanatory context.

Over time, this integration creates a searchable historical archive of code changes, allowing developers to revisit earlier design decisions and understand the reasoning behind specific implementations.

Configurable Settings

Adhoc provides a configuration system that allows developers to tailor the tool to their workflow and environment. Settings such as the LLM provider, API keys, model names, and documentation formats can all be defined within a configuration file.

This configuration is stored locally inside the .Adhoc directory, allowing each project to maintain its own independent settings. Developers can therefore use different models or documentation formats across different repositories without conflict.

Additional configuration options allow developers to personalize generated documentation by specifying metadata such as the author name. This ensures that generated reports maintain a professional and identifiable structure.

Because configuration is managed through simple command-line flags, developers can quickly update settings without needing to edit configuration files manually.

Extensible Design

Adhoc follows a modular architecture that makes it easy to extend or customize. Core components such as change detection, LLM interaction, and document generation are separated into independent modules.

This design allows developers to replace or extend individual components without modifying the entire system. For example, a developer could add support for a new document format, integrate another model provider, or introduce custom prompts tailored to a specific project.

The modular structure also simplifies maintenance and experimentation. New features can be added incrementally without disrupting existing functionality.

As a result, Adhoc is not just a standalone tool but also a framework that can evolve alongside the ecosystem of developer tooling and language models.

## Features
- **Automatic Documentation**: Generates explanations for code changes using Large Language Models (LLMs).
- **Multiple LLM Providers**: Supports both local Ollama models and OpenAI-compatible APIs (OpenAI, OpenRouter, LiteLLM, etc.).
- **Multiple Output Formats**: Supports documentation in LaTeX, Markdown, and Word formats.
- **Version Control Integration**: Detects code changes and commits through simple commands.
- **Configurable Settings**: Allows customization of LLM provider, API keys, output formats, and author information via a configuration file.
- **Extensible Design**: Modular structure makes it easy to extend functionalities.

## Installation
To install Adhoc, you can use pip:
```bash
pip install adhoc-python
```
Ensure you have the necessary dependencies listed in the `requirements.txt` file.

## Usage
Adhoc provides a set of commands to initialize your project, commit changes, generate documentation, and configure settings. Below is an explanation of each command and how to use it.

### Initialization
Before using Adhoc, you need to initialize your project:
```bash
adhoc init --model "your ollama model"
```
**What it does**:
- Creates a `.Adhoc` directory in your project root to store configurations and databases.
- Initializes a SQLite database to track code changes.
- Initializes the whole code to work with your choice of LLM and then generates a codebase summary for context.
- By default, Adhoc uses Ollama for local LLM inference. You can configure it to use OpenAI-compatible APIs (see Configuration section below).

### LLM Provider Configuration
Adhoc supports two types of LLM providers:

1. **Ollama (default)**: Local LLM inference
   ```bash
   adhoc init --model "llama3.1"
   ```

2. **OpenAI-compatible APIs**: Including OpenAI, OpenRouter, LiteLLM, and other model routers
   ```bash
   # First initialize with any model name
   adhoc init --model "gpt-4"
   
   # Then configure for OpenAI or other compatible services
   adhoc config -p openai -k "your-api-key"
   
   # For OpenRouter or custom endpoints
   adhoc config -p openai -k "your-api-key" -e "https://openrouter.ai/api/v1/chat/completions" -m "anthropic/claude-3-opus"
   ```

### Committing Changes
After making changes to your codebase, use the following command to commit those changes and generate explanations:
```bash
adhoc commit -m "Your commit message"
```
**Options**:
- `-m, --message`: (Optional) A commit message describing the changes.

**What it does**:
- Detects changes since the last commit by comparing snapshots.
- Generates explanations for the changes using the LLM, incorporating your commit message if provided.
- Stores the changes and explanations in the database for future reference.

### Generating Documentation
To create documentation of your codebase and its changes, run:
```bash
adhoc generate
```
**What it does**:
- Retrieves the codebase summary and change explanations from the database.
- Generates a documentation file in the format specified in your configuration (latex, markdown, or word).
- The output file (`documentation.tex`, `documentation.md`, or `documentation.docx`) is created in your project directory.

### Configuration
Customize Adhoc settings using the `adhoc config` command:
```bash
adhoc config -d md -u "Your Name"
```
**Options**:
- `-d, --document-format`: Sets the output document format. Accepts `md` for Markdown, `tex` for LaTeX, or `word` for Word documents.
- `-u, --username`: Sets the author name to be used in the documentation.
- `-p, --provider`: Sets the LLM provider. Accepts `ollama` (default) or `openai` for OpenAI-compatible APIs.
- `-k, --api-key`: Sets the API key for OpenAI-compatible services.
- `-e, --api-endpoint`: Sets a custom API endpoint for model routers (e.g., OpenRouter, LiteLLM).
- `-m, --model`: Sets the model name to use.

**What it does**:
- Updates the configuration file (`config.json` in the `.Adhoc` directory) with your preferences.
- The changes affect how documentation is generated and personalized.

**Examples**:
```bash
# Configure for local Ollama usage
adhoc config -d word -u "Shreyas"

# Configure for OpenAI
adhoc config -p openai -k "your-openai-api-key" -m "gpt-4"

# Configure for OpenRouter (or other OpenAI-compatible model routers)
adhoc config -p openai -k "your-openrouter-api-key" -e "https://openrouter.ai/api/v1/chat/completions" -m "anthropic/claude-3-opus"

# Configure for LiteLLM or other custom endpoints
adhoc config -p openai -k "your-api-key" -e "https://your-service.com/v1/chat/completions" -m "your-model"
```

## Requirements
- Python 3.6 or higher

**Dependencies**:
- `jinja2`
- `requests`
- `watchdog`
- `python-docx` (for Word document generation)

Additional dependencies are listed in `requirements.txt`.

## Contributing
Contributions are welcome! Please open an issue or submit a pull request on GitHub.

## License
This project is licensed under the MIT License. See the LICENSE file for details.

## A Reflective Note
> "Life is an ad hoc affair. It has to be improvised all the time because of the hard fact that everything we do changes what is. This is distressing to people who would like to see things beautifully planned out and settled once and for all. That cannot be."  
> ― Jane Jacobs


Happy coding!
