# stampthisfile MCP server

A hosted [MCP](https://modelcontextprotocol.io) server that lets AI agents
timestamp files in Bitcoin with [stampthisfile](https://stampthisfile.com):
send the SHA-256 hash of a file (never the file), pay per hash in USDC with
[x402](https://x402.org), and get a standard [OpenTimestamps](https://opentimestamps.org)
`.ots` proof that anyone can verify without us. No account or API key.

- Endpoint: `https://stampthisfile.com/mcp` (Streamable HTTP, stateless)
- Auth: none. Stamping is paid per call with x402; everything else is free.

There is nothing to install or run: this repository describes the hosted
server. The server itself is not open source.

## Connect

Claude Code:

```sh
claude mcp add --transport http stampthisfile https://stampthisfile.com/mcp
```

Cursor, VS Code and other clients (`.mcp.json` style):

```json
{
  "mcpServers": {
    "stampthisfile": {
      "type": "http",
      "url": "https://stampthisfile.com/mcp"
    }
  }
}
```

## Tools

| Tool | What it does |
| --- | --- |
| `stamp_hashes` | Timestamp 1-100 SHA-256 hashes in Bitcoin. Paid per hash with x402; returns a pending `.ots` proof per hash and a signed receipt |
| `get_stamp` | A stamp's status: pending, complete (with the Bitcoin block and time) or failed |
| `get_proof` | The current `.ots` proof, base64. Fetch it again once complete to get the Bitcoin attestation |
| `verify_proof` | Check any OpenTimestamps proof against a file's SHA-256 |

A proof is pending at first and completes once Bitcoin confirms it, usually
within 1-6 hours. A complete proof shows the file existed no later than the
time of its Bitcoin block. It is not a signature and does not show who made the
file: read [stampthisfile.com/evidence.md](https://stampthisfile.com/evidence.md)
before stamping something meant as evidence.

## Pricing

Stamping costs a fixed price per hash, in USDC on Base, paid with x402 using
the [MCP transport](https://github.com/coinbase/x402): without a payment,
`stamp_hashes` returns a tool error whose `structuredContent` is the x402
`PaymentRequired`. Retry with the signed `PaymentPayload` in
`_meta["x402/payment"]`, or in the `payment` argument if your client cannot set
`_meta`. The settlement comes back in `_meta["x402/payment-response"]`. Invalid
input is never charged. Reading, downloading and verifying proofs are free.

## More

- [stampthisfile.com/llms.txt](https://stampthisfile.com/llms.txt): the API for agents
- [stampthisfile.com/openapi.json](https://stampthisfile.com/openapi.json): REST API
- [stampthisfile/skills](https://github.com/stampthisfile/skills): the same, as agent skills
  (`npx skills add stampthisfile/skills`)

## License

MIT, for the contents of this repository.
