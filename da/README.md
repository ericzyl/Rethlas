# DA runtime adapter contract

This directory documents the small, runtime-neutral surface DeepAnalyze's
`plugins/rethlas` adapter relies on. The math prompts, skills and search
behaviour stay in this repository; only host-owned paths and the verifier
transport are configurable.

## Environment

| Variable | Required | Meaning |
| --- | --- | --- |
| `RETHAS_MEMORY_ROOT` | yes in DA | Absolute root for all MCP memory namespaces. DA sets it to `<DATA_DIR>/rethlas/loops`. |
| `RETHAS_VERIFICATION_RESULTS_ROOT` | yes in DA | Absolute root for verification outputs. DA sets it to `<DATA_DIR>/rethlas/loops`. |
| `RETHAS_MATLAS_URL` | no | Override the Matlas theorem-search endpoint. |
| `RETHAS_VERIFY_URL` | no | Standalone verification endpoint; DA does not use it. |

The MCP tools keep their original `problem_id` / `run_id` namespaces. The DA
adapter passes `<loopId>/generation` for the generation memory and
`<loopId>/verification/<verificationId>` for each verification, so loops never
share a memory directory.

## Process shape

DA starts two stdio MCP servers from the plugin:

- `agents/generation/mcp/server.py` — generation memory, Matlas search,
  branch state; the standalone `verify_proof_service` is unused by DA.
- `agents/verification/mcp/server.py` — verification memory, Matlas search,
  schema validation and `verification.json` output.

DA owns the loop, sessions, model calls and the `rethlas_verify` tool. This
repository does not spawn Codex in the DA path.
