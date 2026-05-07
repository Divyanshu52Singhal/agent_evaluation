# Agent Evaluation Framework

## Possible Features
- Interactive web UI / dashboard to visualize results and drill into scenario traces
- Support for additional LLM providers (OpenAI, Anthropic, Cohere) and local LLMs
- Docker image + CI workflows for reproducible evaluations
- Scenario marketplace / shared scenario repository
- Advanced metrics and visualizations (charts, trend history)
- Real-time / streaming evaluation and monitoring
- Plugin system for custom evaluators, judges, and security scanners
- Integration with vector DBs or dataset stores for large-scale benchmarking
- HTML/PDF export of rich reports

## Overview

This repository provides a small framework to evaluate conversational agents using
black-box, white-box and gray-box testing modes. It runs scenario-driven tests,
captures agent responses (calls or HTTP endpoints), applies scoring and optional
LLM-based judgement, and produces a JSON evaluation report.

Key capabilities:
- Run scenario suites against a Python agent or HTTP endpoint
- Caching of agent responses to avoid re-invocation
- Optional Azure OpenAI LLM judge for semantic scoring
- Security scanning hooks and aggregated scoring

## Requirements

Install dependencies:

```bash
pip install -r requirements.txt
```

Environment variables (optional, for LLM judge / caching):
- `AZURE_OPENAI_ENDPOINT`, `AZURE_OPENAI_KEY`, `AZURE_OPENAI_DEPLOYMENT`

## Quick Usage

Run the CLI (see `main.py`):

```bash
python main.py --agent examples/sample_agent.py
python main.py --agent examples/sample_agent.py --scenarios examples/sample_scenarios.yaml
python main.py --agent examples/sample_agent.py --modes black_box gray_box --output report.json
python main.py --endpoint http://localhost:8000/agent --name "MyAgent"
python main.py --agent examples/sample_agent.py --llm-judge
```

Common CLI options:
- `--agent FILE` : Path to a Python file that defines `agent_invoke(input, context) -> dict`
- `--endpoint URL` : HTTP endpoint accepting POST {input, context}
- `--scenarios FILE` : YAML scenario file (defaults provided)
- `--modes` : `black_box`, `white_box`, `gray_box`
- `--output` : Path to JSON report
- `--llm-judge` : Enable Azure OpenAI-based judge

## Writing an Agent

Your Python agent file must expose a function:

```python
def agent_invoke(input: str, context: dict) -> dict:
    # return a dict containing at least {'output': '...'}
    return {"output": "Hello"}
```

Or run an HTTP agent endpoint that accepts `POST` JSON {"input": ..., "context": ...}

## Project Structure

- `main.py` - CLI entrypoint
- `agent_eval/` - core framework package
  - `core/` - base classes and runner orchestration
  - `evaluators/` - black/white/gray evaluators
  - `judges/` - optional LLM judge (Azure OpenAI)
  - `cache/` - result caching and report storage
  - `reports/` - report generation and printing
  - `scenarios/` - scenario management and YAML loading
  - `security/` - security scanner hooks
- `examples/` - sample agents and scenarios

## Output

Default output file is `evaluation_report.json`. The CLI prints a summary
and saves the full report to the path provided by `--output`.

## Contributing

Contributions welcome. Please open issues or PRs for bugfixes and feature requests.

## License

This project does not contain a license file. Add a license if you plan to open-source it.
