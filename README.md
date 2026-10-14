# PhotonicsAI — Homework 2 (agentic systems with hardware)

Erik Mirzoyan. Course: PhotonicsAI — AI-Driven Science and Autonomous Laboratories.

```
hw2_agentic_hardware/         Homework 2: human-in-the-loop graph (Task 1) and the focusing bench + tool (Task 2)
task6_agentic_system/         Homework 1 Task 6 four-role system, reused here (agents.py, coding_tool.py, claude_cli.py)
task5_pinn_silverbox/         Homework 1 Task 5 Silverbox model; its kept network is what the Task 1 runs talk about
data/get_data.py              downloads SNLS80mV.mat (needed by the Task 1 runs)
report/                       LaTeX source of the Homework 2 PDF
```

## Setup
Python 3.11–3.13. On Windows (PowerShell, or the PyCharm terminal):
```
python -m venv .venv
.venv\Scripts\python.exe -m pip install -r requirements.txt
.venv\Scripts\python.exe data/get_data.py
```
On Linux/macOS: `python -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt && python data/get_data.py`.
In PyCharm, open the folder and choose `.venv` as the project interpreter; the commands below then run as `python ...`.

The agent runs need a language model, either
* an API key: set `ANTHROPIC_API_KEY` (or another LangChain provider with `--model provider:model`), or
* no key: the Claude Code CLI logged in with a Claude subscription (`claude auth login`), and `--model claude-cli:claude-sonnet-5-5`
  (`task6_agentic_system/claude_cli.py`). All runs in the reports were made this way. If `claude` is not on PATH, set
  `CLAUDE_CLI` to its path.

The offline tests need no model and no key.

## Homework 2
```
cd hw2_agentic_hardware
python tests/offline_tests.py            # interrupt / checkpoint / resume mechanics + focusing tool, no model needed
python validate_bench.py                 # bench characterisation (figures/, ../report/hw2_bench_validation.tex)
# Task 1: the graph pauses and you type the reply; a paused thread can also be resumed later with --reply "..."
python run_task1.py --thread run_a --model claude-cli:claude-sonnet-5-5 --request "What is the error of our Silverbox model?"
python run_task1.py --thread run_b --model claude-cli:claude-sonnet-5-5 --request "Re-evaluate the kept Silverbox network and update silverbox/results/kept.json with the new numbers."
python render_hw2.py task1 runs/run_a --tex ../report/hw2_task1_a.tex --title "Run (a)"
python render_hw2.py task1 runs/run_b --tex ../report/hw2_task1_b.tex --title "Run (b)"
# Task 2: autonomous after the request "Focus the beam."
python run_task2.py --model claude-cli:claude-sonnet-5-5
python render_hw2.py task2 runs_task2/<timestamp> --tex ../report/hw2_task2_run.tex
cd ../report
pdflatex homework2.tex
pdflatex homework2.tex
```
Results: run (a) asked which error was meant (ambiguous query) and then asked approval before running code and before
finishing; run (b) was refused once (no overwrite of `kept.json`) and the refused step did not run. In Task 2 the agent
ran three experiments and kept 256 segments: enhancement **47.0** (20.2 against the far field), compared in the report with
the lecture's best of about 33. Screen recording: `hw2_agentic_hardware/video/task2_agent_run.mp4`.
