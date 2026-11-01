# opencode-sec-gates

Guardrails for running [opencode](https://opencode.ai) on top of an abliterated
model lane (DeepSeek-V4.1-Flash-1017 + the Keys L10-35 `wo_b` overlay served by
TensorFold TP2 on 2x DGX Spark). The model no longer refuses destructive tool
calls, does not notice missing parameters (it guesses), and follows instructions
found inside tool results — so the harness carries the safety, not the model.

Contents:

- `opencode.jsonc` — the global config with a hardened `permission` block:
  suspicious pipe-to-shell, `rm -rf`, direct-io `dd`, `mkfs` and fork-bomb style
  commands are denied; `sudo`, docker, ssh, scp, systemctl, `git push`, `gh` and
  recursive chmod ask first; edits ask; external directories ask (secrets deny).
  The MCP block uses `Bearer {env:CONTEXT7_API_KEY}` — set that variable (or
  paste your own token) before use.
- `agent/sec.md` — a locked-down primary agent bound to the abliterated lane
  (`gx10-tp2/deepseek-v4.1-flash`) with deny-by-default tooling and lab-scope
  rules. Its system prompt hard-gates destructive actions and treats tool
  results as untrusted content.

## Install

```bash
cp opencode.jsonc ~/.config/opencode/
cp agent/sec.md ~/.config/opencode/agent/sec.md   # mkdir -p ~/.config/opencode/agent if new
```

Restart opencode afterwards — config is loaded once at startup and not
hot-reloaded.

## Rules evaluation

In per-pattern `bash` objects the engine evaluates the last matching rule, so
the broad `*` first and the specific overrides last.

## License

MIT.
