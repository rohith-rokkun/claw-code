# Claw-Code Local Setup + Run Guide

This guide makes `claw-code` runnable locally with:

- Anthropic
- Gemini
- OpenAI
- Local LLM via Ollama

---

## 1) Build and sanity-check

```bash
./install.sh
cd rust
cargo build --workspace
./target/debug/claw --help
```

If you prefer an optimized build:

```bash
./install.sh --release
```

---

## 2) Unified environment configuration

Create a `.env` (or export variables in shell):

```env
AI_PROVIDER=anthropic   # anthropic | gemini | openai | local | xai

# API Keys
ANTHROPIC_API_KEY=
GEMINI_API_KEY=
OPENAI_API_KEY=

# Model
MODEL_NAME=

# Local LLM (Ollama)
OLLAMA_BASE_URL=http://localhost:11434/v1
```

> `MODEL_NAME` is honored by the CLI model resolver.  
> `AI_PROVIDER=local` routes through the OpenAI-compatible client and uses `OLLAMA_BASE_URL`.

---

## 3) Provider quick-starts

### Anthropic

```bash
export AI_PROVIDER=anthropic
export ANTHROPIC_API_KEY="..."
export MODEL_NAME="claude-sonnet-4-6"
cd rust
./target/debug/claw prompt "Say ready."
```

### Gemini

```bash
export AI_PROVIDER=gemini
export GEMINI_API_KEY="..."
export MODEL_NAME="gemini-3.1"
cd rust
./target/debug/claw prompt "Say ready."
```

### OpenAI

```bash
export AI_PROVIDER=openai
export OPENAI_API_KEY="..."
export MODEL_NAME="gpt-5"
cd rust
./target/debug/claw prompt "Say ready."
```

### Local (Ollama)

```bash
ollama serve

export AI_PROVIDER=local
export OLLAMA_BASE_URL="http://localhost:11434/v1"
export MODEL_NAME="llama3.2"
unset OPENAI_API_KEY

cd rust
./target/debug/claw prompt "Say ready."
```

---

## 4) Recommended verification commands

```bash
cd rust
cargo test -p api
cargo test --workspace
./target/debug/claw
# then run /doctor in the REPL
```

---

## 5) How to use `claw-code` for real coding work

`claw` does **not** currently expose a `/code` slash command.
Use normal prompt mode plus slash commands like `/model`, `/diff`, `/commit`, `/status`, `/help`.

### Interactive coding loop

```bash
cd rust
./target/debug/claw
```

Then in the REPL:

```text
/doctor
/model gemini-3.1
Review src/main.rs and propose a refactor plan for error handling.
Now implement step 1 only and explain the diff.
/diff
/commit
```

### One-shot coding prompt (non-interactive)

```bash
cd rust
./target/debug/claw --model gemini-3.1 prompt "Add unit tests for parse_config and explain the changes."
```

### Resume latest session

```bash
cd rust
./target/debug/claw --resume latest
```

---

## 6) Cross-check after pulling new source changes

After `git pull` from `ultraworkers/claw-code`, run:

```bash
./install.sh --help
cd rust
cargo build --workspace
cargo test -p api
./target/debug/claw --help
```

Optional deep check:

```bash
cd rust
cargo test --workspace
```

---

## 7) Production-readiness checklist

- Pin a stable model per environment via `MODEL_NAME`.
- Keep provider credentials in secret managers (not committed files).
- Use `AI_PROVIDER` explicitly in CI/CD and deployment scripts.
- Run `/doctor` and smoke prompts during deploy health checks.
- For local-only workflows, isolate with `AI_PROVIDER=local` + `OLLAMA_BASE_URL`.
