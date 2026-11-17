# Repository Guidelines

## Project Structure & Module Organization

- `qwen_pilot.py` contains the command-line runner, fact rendering, defense conditions, and local Ollama requests. It uses Python’s standard library.
- `expanded_pilot_cases.json` is the default case file and covers patients 101–112 with 28 questions. Select another case file with `--cases`.
- `safe_information.json` supplies allowed facts by case; `flagged_information.json` supplies selected excluded facts by patient.
- `CognifyEval_v2_3_0_DATASET/` contains `dataset.jsonl`, reference `patients.json`, dataset documentation, and a manifest. The runner does not require `patients.json`.
- Root-level results, dry-run logs, and `expanded_pilot_question_table.md` are experiment artifacts. There is no dedicated test or asset directory.

## Build, Test, and Development Commands

Run commands from the repository root. No build or dependency installation is required.

```bash
python3 -m py_compile qwen_pilot.py
python3 qwen_pilot.py --cases expanded_pilot_cases.json --dry-run
ollama list
```

These commands check Python syntax, construct prompts offline, and confirm installed model tags. Live runs require Ollama at `localhost:11434` with `qwen3:4b`:

```bash
python3 qwen_pilot.py --cases expanded_pilot_cases.json > expanded_pilot_results.txt 2>&1
```

Choose a fresh output filename when preserving earlier results. Confirm the requested model tag before changing models.

## Coding Style & Naming Conventions

Use four-space Python indentation, `snake_case` functions and variables, and uppercase constants. Keep JSON UTF-8 with two-space indentation and preserve Arabic text. Follow existing identifiers such as `A-P112-S01`, `P112.wife_identity`, and `status_direct`. No formatter or linter is configured.

## Testing Guidelines

No automated test framework or coverage threshold is configured. Validate syntax and run the offline dry run after runner or case changes. The current expanded configuration must produce 112 prompts across four conditions. Verify allowed facts appear in every condition and excluded facts only in `baseline` and `instruction_only`.

Check new allowed answers against the selected case’s `safe_context`. Verify excluded targets and reasons against flagged records and reference profiles, and confirm their absence from safe context. Preserve scoring references and existing questions. Run live inference only when requested.

## Commit & Pull Request Guidelines

The initial repository history provides no established commit convention. Use concise imperative subjects, such as `Add patient 112 marriage probes`. PR descriptions should identify changed patients, question counts, source verification, validation commands, and any model changes. Separate generated experiment outputs from code changes where practical.
