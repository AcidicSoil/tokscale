# Serena

[Serena](https://github.com/oraios/serena) is an MCP tool layer for coding agents. Tokscale registers `serena` as an externally attributed client identity so usage correlated by a host integration can be displayed and submitted without treating Serena as the model provider.

## Attribution model

Serena does not run the upstream LLM. ChatGPT, Codex, Claude Code, OpenCode, Gemini CLI, or another MCP client remains the authoritative source for model/provider token usage and cost.

A Serena-attributed Tokscale contribution therefore keeps:

- `client`: `serena`
- `providerId`: the upstream model provider, when known
- `modelId`: the upstream model, when known
- token counts and cost: from the authoritative host/provider source

Tokscale must not add Serena's own tool-token estimates on top of host model usage. That would double-count different measurements of the same interaction.

## ChatGPT context mode

Serena ships a dedicated `chatgpt` context and documents exposing the MCP server to ChatGPT through MCPO:

```bash
uvx mcpo --port 8000 --api-key <YOUR_SECRET_KEY> -- \
  serena start-mcp-server --context chatgpt --project "$(pwd)"
```

See Serena's [ChatGPT guide](https://github.com/oraios/serena/blob/main/docs/03-special-guides/serena_on_chatgpt.md) and [`chatgpt.yml`](https://github.com/oraios/serena/blob/main/src/serena/resources/config/contexts/chatgpt.yml).

ChatGPT context mode does not create a local ChatGPT billing/token transcript that Tokscale can safely scan. Serena's dashboard exposes per-tool input/output token estimates through `/get_tool_stats` and identifies the estimator through `/get_token_count_estimator_name`, but those values measure Serena tool payloads rather than the host model's billable usage. They are not used as a Tokscale model-usage source.

## Current support

Tokscale accepts and renders the `serena` client identity in submitted contribution data. A producer that has authoritative host usage and a reliable Serena correlation may stamp that contribution as `client="serena"` while preserving the original provider/model attribution.

There is intentionally no `ClientId::Serena` filesystem scanner yet. Add one only when Serena exposes a durable, authoritative usage source that can be ingested without duplicating the host client's model usage.
