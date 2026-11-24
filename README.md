# llm-meter

> Meter your LLM token usage across OpenAI, Anthropic & Gemini — record what each call costs and see **latest vs previous** at a glance. Zero-dependency CLI.

`llm-meter` is a tiny CLI that logs the token usage of your LLM calls and reports how it's trending. When you call an LLM, the API response already tells you exactly how many tokens you used — `llm-meter` captures that into a local log and shows your latest usage next to the previous one, per provider and interaction kind.

## Why

Token usage is cost, and it creeps up quietly — a longer system prompt here, an extra tool call there. `llm-meter` makes each call's usage visible and comparable, so you notice regressions before the bill does. No dashboards, no accounts, no dependencies — one command.

## Requirements

- Node.js >= 18

## Install

No install needed:

```bash
npx @stackgauge/llm-meter report
```

Or globally:

```bash
npm install -g @stackgauge/llm-meter
llm-meter report
```

## Usage

### Record a usage event

Pass counts explicitly, or **pipe the API response's usage object straight in** — field names are normalized per provider:

```bash
# Explicit counts
llm-meter record --provider openai --kind ask --input 1200 --output 340

# Pipe an OpenAI usage object
echo '{"prompt_tokens":1500,"completion_tokens":500}' \
  | llm-meter record --provider openai --kind agent

# Pipe a full response — the `usage` / `usageMetadata` field is picked up automatically
your-anthropic-call | llm-meter record --provider anthropic --kind plan
```

Field mapping: OpenAI (`prompt_tokens`/`completion_tokens`/`total_tokens`), Anthropic (`input_tokens`/`output_tokens`), Gemini (`promptTokenCount`/`candidatesTokenCount`/`totalTokenCount`). Any other provider works via explicit `--input/--output/--total`.

### Report latest vs previous

```bash
llm-meter report
```

```
LLM token usage — latest vs previous
  PROVIDER   KIND   LATEST     PREVIOUS   CHANGE     RUNS   LIFETIME
  anthropic  agent  1,020      —          —          1      1,020
  openai     ask    2,000      1,540      +460       2      3,540
```

Filter with `--provider`, and add `--json` for machine-readable output.

## Commands & options

```
llm-meter record     Record a usage event (explicit counts or piped response JSON)
llm-meter report     Show latest vs previous token usage per provider + kind

--provider <name>    openai | anthropic | gemini | cursor | windsurf | ...
--kind <name>        request | ask | plan | agent (free-form; default request)
--input/--output n   Explicit prompt/completion token counts
--total <n>          Explicit total token count
--model <name>       Optional model name to tag the event
--file <path>        Usage file (default ~/.llm-meter/usage.jsonl, or $LLM_METER_USAGE)
--json               Machine-readable output
-h, --help           Show help
-v, --version        Show version
```

## What it can and can't see

- **OpenAI, Anthropic (Claude API), and Gemini** report token usage in their responses, so if you're the one calling the API you can capture it automatically.
- **Cursor, Claude Desktop, ChatGPT and other GUI apps do not expose per-request token counts** to external tools. You can still record those manually from their own usage dashboard, e.g. `llm-meter record --provider cursor --kind agent --total 5000`.

## Data

Events are stored as append-only JSONL at `~/.llm-meter/usage.jsonl` (override with `--file` or `$LLM_METER_USAGE`). It's a plain local file you can inspect, back up, or delete.

## Development

```bash
npm install
npm run build
npm run lint
npm test
```

## Author

Created and maintained by **Sandip Gami** ([@next-dna](https://github.com/next-dna)).

## License

MIT © Sandip Gami
