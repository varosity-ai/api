# MCP Reference Architecture for Varosity

**Status:** Production-ready  
**Last updated:** 2026-05-15  

This guide documents Varosity's MCP server design, implementation patterns, and best practices. Use this as a reference when building your own MCP servers or extending the Varosity MCP integration.

---

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Tool Discovery Protocol](#tool-discovery-protocol)
3. [Authentication & Authorization](#authentication--authorization)
4. [Error Handling & Recovery](#error-handling--recovery)
5. [Skill Self-Update Mechanism](#skill-self-update-mechanism)
6. [Testing & Verification](#testing--verification)
7. [Deployment & Operations](#deployment--operations)
8. [Common Patterns](#common-patterns)

---

## Architecture Overview

### Core Principles

1. **Stateless Server** — No session state on the server. All auth is bearer token in headers.
2. **Proxy Pattern** — MCP server proxies all requests to the backend API over HTTPS.
3. **Zero Runtime Dependencies** — Single Node.js process, no databases, no caches.
4. **Graceful Degradation** — If a tool fails, others remain available.
5. **Self-Describing** — Tool schema is the source of truth; docs generated from it.

### High-Level Flow

```
Claude Desktop / Cursor / Hermes
        ↓ (MCP protocol)
  Varosity MCP Server (stdio)
        ↓ (HTTP/2 + bearer token)
  varosity.ai/api/mcp
        ↓
  Varosity Backend (payment, routing, logging)
```

**Key invariant:** The MCP server is a thin proxy. All business logic lives in the backend API.

---

## Tool Discovery Protocol

### How It Works

When an MCP host starts the Varosity server, it asks "what tools do you have?" The server responds with:

```json
{
  "tools": [
    {
      "name": "generate_video",
      "description": "Generate a video from a prompt...",
      "inputSchema": {
        "type": "object",
        "properties": {
          "prompt": { "type": "string" },
          "durationSec": { "type": "number" }
        },
        "required": ["prompt"]
      }
    },
    ...
  ]
}
```

### Discovery Implementation

**File:** `src/index.ts`

```typescript
const TOOL_SCHEMAS = [
  {
    name: "generate_video",
    description: "Generate a video from a text prompt",
    inputSchema: {
      type: "object",
      properties: {
        prompt: { type: "string", description: "Video description" },
        durationSec: { type: "number", description: "Video length (1-60s)" },
        modelId: { type: "string", description: "Model (kling-1, veo-3, etc.)" }
      },
      required: ["prompt"],
    }
  },
  // ... more tools
];

// Respond to tools/list
server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: TOOL_SCHEMAS
}));
```

### Updating Tool Definitions

**When:** Whenever the backend API adds a new tool or parameter.

**Process:**
1. Backend deploys new tool to `varosity.ai/api/mcp`
2. MCP server pulls latest schema on startup (via `/api/mcp/schema`)
3. No server restart required—agents discover new tools next session

**Implementation:**

```typescript
async function fetchToolSchemas() {
  const response = await fetch("https://varosity.ai/api/mcp/schema", {
    headers: { "Authorization": `Bearer ${process.env.VAROSITY_API_KEY}` }
  });
  return response.json();
}
```

---

## Authentication & Authorization

### Bearer Token Pattern

All requests to the backend API use HTTP bearer token authentication.

```
Authorization: Bearer vsk_live_abc123...
```

**Token structure:** `vsk_live_[uuid]` (live) or `vsk_test_[uuid]` (test)

### Where the Token Lives

1. **MCP host config** — User stores token in `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "varosity": {
      "command": "npx",
      "args": ["-y", "@varosity/mcp-server"],
      "env": {
        "VAROSITY_API_KEY": "vsk_live_..."
      }
    }
  }
}
```

2. **MCP server process** — Reads from `process.env.VAROSITY_API_KEY`

3. **Every HTTP request** — Attached to Authorization header:
```typescript
const headers = {
  "Authorization": `Bearer ${process.env.VAROSITY_API_KEY}`,
  "Content-Type": "application/json",
  "User-Agent": "varosity-mcp/0.1.0"
};
```

### Token Validation Flow

```
1. Host initializes MCP server, passes VAROSITY_API_KEY in env
2. MCP server reads env var
3. MCP server makes request to backend: Authorization: Bearer $TOKEN
4. Backend validates token:
   - Signature check (if JWToken)
   - Active status check
   - Rate limit check
5. If valid → tool call proceeds
   If invalid → 401 Unauthorized, MCP host shows error
```

### Best Practices

- **Never log tokens** — Even in debug mode
- **Never return tokens** — From any API response
- **Use environment variable** — Never hardcode in config files
- **Rotate tokens** — If compromised, use the Studio dashboard
- **Test tokens** — Use `vsk_test_*` for development

---

## Error Handling & Recovery

### Error Categories

| Category | HTTP Status | Recovery |
|----------|------------|----------|
| **Auth failure** | 401 | User must check API key |
| **Permission denied** | 403 | User exceeded quota or plan limit |
| **Invalid input** | 400 | MCP host should validate schema |
| **Rate limited** | 429 | Exponential backoff (1s → 2s → 4s) |
| **Backend error** | 500 | Retry with exponential backoff |
| **Network timeout** | - | Retry after 2s delay |

### MCP Error Response Format

```json
{
  "content": [
    {
      "type": "text",
      "text": "[ERROR] 401 Unauthorized: Invalid API key. Check VAROSITY_API_KEY in your config."
    }
  ],
  "isError": true
}
```

### Implementation: Exponential Backoff

```typescript
async function callBackendWithRetry(endpoint, payload, maxRetries = 3) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const response = await fetch(`https://varosity.ai${endpoint}`, {
        method: "POST",
        headers: { "Authorization": `Bearer ${process.env.VAROSITY_API_KEY}` },
        body: JSON.stringify(payload)
      });

      if (response.status === 429) {
        // Rate limited — back off exponentially
        const delay = Math.pow(2, attempt) * 1000;
        await new Promise(resolve => setTimeout(resolve, delay));
        continue;
      }

      if (!response.ok) {
        const error = await response.json();
        return { error: error.message, status: response.status };
      }

      return response.json();
    } catch (e) {
      if (attempt === maxRetries - 1) throw e;
      await new Promise(resolve => setTimeout(resolve, 2000));
    }
  }
}
```

### Common Error Scenarios

#### 1. Missing API Key
```
Error: process.env.VAROSITY_API_KEY is undefined

Fix: Add to MCP config:
  "env": { "VAROSITY_API_KEY": "vsk_live_..." }
```

#### 2. Invalid Token Format
```
Error: 401 Unauthorized: Invalid bearer token format

Fix: Token must be vsk_live_* or vsk_test_*
     Check you're not using an old format
```

#### 3. Quota Exceeded
```
Error: 403 Forbidden: Plan limit reached (1000/month videos)

Fix: Upgrade plan or wait for quota reset
     Contact support for emergency quota bump
```

#### 4. Network Timeout
```
Error: ECONNRESET after 30s

Fix: Check internet connection
     Retry in 2-5 seconds
     Check varosity.ai/status for incidents
```

---

## Skill Self-Update Mechanism

### Why Self-Update?

When Varosity deploys a new tool (e.g., `generate_music`), all agents should know about it **without restarting**. The MCP server fetches the latest tool schema on every startup.

### Update Flow

1. **Backend deploys** new tool to production
2. **Agent restarts** (or connects for first time)
3. **MCP server starts** → fetches `/api/mcp/schema`
4. **Agent sees new tool** in `tools/list` response
5. **Agent can use it** immediately

### Schema Endpoint

**URL:** `https://varosity.ai/api/mcp/schema`  
**Method:** GET  
**Auth:** Bearer token required

**Response:**
```json
{
  "version": "0.1.5",
  "tools": [
    {
      "name": "generate_video",
      "description": "...",
      "inputSchema": { ... }
    },
    {
      "name": "generate_voice",
      "description": "...",
      "inputSchema": { ... }
    },
    // ... all current tools
  ]
}
```

### Implementation

```typescript
let toolSchemas = [];

async function loadToolSchemas() {
  try {
    const url = new URL("https://varosity.ai/api/mcp/schema");
    const response = await fetch(url, {
      headers: { "Authorization": `Bearer ${process.env.VAROSITY_API_KEY}` }
    });
    
    if (!response.ok) {
      console.error(`Failed to fetch schemas: ${response.status}`);
      // Fall back to hardcoded schemas if fetch fails
      return loadDefaultSchemas();
    }
    
    const data = await response.json();
    toolSchemas = data.tools;
    console.log(`Loaded ${toolSchemas.length} tools from backend`);
  } catch (error) {
    console.error("Error fetching schemas, using defaults:", error);
    toolSchemas = loadDefaultSchemas();
  }
}

// On server start
await loadToolSchemas();

// Respond to tools/list
server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: toolSchemas
}));
```

### Version Pinning (Optional)

For stability, you can pin to a specific schema version:

```typescript
const SCHEMA_VERSION = "0.1.5";

const response = await fetch(
  `https://varosity.ai/api/mcp/schema?version=${SCHEMA_VERSION}`,
  { headers: { "Authorization": `Bearer ${process.env.VAROSITY_API_KEY}` } }
);
```

---

## Testing & Verification

### Unit Tests

**File:** `src/__tests__/index.test.ts`

```typescript
import { describe, it, expect } from "@jest/globals";

describe("MCP Server", () => {
  it("should load tool schemas on startup", async () => {
    const schemas = await loadToolSchemas();
    expect(schemas.length).toBeGreaterThan(0);
    expect(schemas[0]).toHaveProperty("name");
    expect(schemas[0]).toHaveProperty("inputSchema");
  });

  it("should handle missing VAROSITY_API_KEY gracefully", async () => {
    const oldKey = process.env.VAROSITY_API_KEY;
    delete process.env.VAROSITY_API_KEY;
    
    const schemas = await loadToolSchemas();
    expect(schemas).toBeDefined(); // Falls back to defaults
    
    process.env.VAROSITY_API_KEY = oldKey;
  });

  it("should format bearer token correctly", () => {
    process.env.VAROSITY_API_KEY = "vsk_test_abc123";
    const header = getAuthHeader();
    expect(header).toBe("Bearer vsk_test_abc123");
  });
});
```

### Integration Tests

**File:** `src/__tests__/integration.test.ts`

```typescript
describe("MCP Integration", () => {
  it("should respond to tools/list request", async () => {
    const server = createMCPServer();
    const response = await server.handleRequest({
      jsonrpc: "2.0",
      id: 1,
      method: "tools/list"
    });

    expect(response.result.tools).toBeDefined();
    expect(Array.isArray(response.result.tools)).toBe(true);
  });

  it("should proxy tool calls to backend API", async () => {
    const result = await server.handleRequest({
      jsonrpc: "2.0",
      id: 2,
      method: "tools/call",
      params: {
        name: "generate_video",
        arguments: { prompt: "test video" }
      }
    });

    expect(result.result).toHaveProperty("jobId");
  });

  it("should handle 401 errors with helpful message", async () => {
    process.env.VAROSITY_API_KEY = "invalid";
    
    const result = await server.handleRequest({
      jsonrpc: "2.0",
      id: 3,
      method: "tools/call",
      params: { name: "generate_video", arguments: { prompt: "test" } }
    });

    expect(result.error).toBeDefined();
    expect(result.error.code).toBe(401);
  });
});
```

### Smoke Tests (CLI)

**File:** `bin/smoke-test.mjs`

```bash
#!/usr/bin/env node

const { spawn } = require("child_process");
const http = require("http");

console.log("Starting MCP server...");
const server = spawn("npx", ["--eval", "import('./dist/index.js')"]);

setTimeout(() => {
  console.log("✓ Server started");
  server.kill();
  process.exit(0);
}, 2000);

server.on("error", (err) => {
  console.error("✗ Server failed:", err);
  process.exit(1);
});
```

### Manual Testing in Claude Desktop

1. Add to `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "varosity-test": {
      "command": "node",
      "args": ["/path/to/varosity-mcp/dist/index.js"],
      "env": {
        "VAROSITY_API_KEY": "vsk_test_..."
      }
    }
  }
}
```

2. Restart Claude Desktop

3. In conversation, ask: "What tools from Varosity do you have access to?"

4. Expected response: Lists all tools with descriptions

5. Try: "Generate a 5-second test video with the prompt 'a cat playing piano'"

---

## Deployment & Operations

### Publishing to npm

```bash
# Build
pnpm build

# Test
pnpm test

# Publish (first time)
npm publish --access public

# Publish (updates)
npm version patch  # or minor, major
npm publish
```

**Package registry:** https://www.npmjs.com/package/@varosity/mcp-server

### Self-Hosting

If you want to run your own Varosity MCP server (not from npm):

```bash
# Clone
git clone https://github.com/varosity-ai/mcp-server.git
cd mcp-server

# Install
pnpm install

# Build
pnpm build

# Run
VAROSITY_API_KEY=vsk_live_... node dist/index.js
```

### Pointing at Different Backend

```bash
VAROSITY_API_KEY=vsk_test_...
VAROSITY_API_BASE=https://staging.varosity.ai
node dist/index.js
```

### Monitoring & Debugging

**Enable debug logging:**
```bash
DEBUG=varosity:* node dist/index.js
```

**Health check:**
```bash
# Server should respond to stdio on connection
echo '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' | node dist/index.js | head -20
```

---

## Common Patterns

### Pattern 1: Handling Long-Running Renders

```typescript
// User calls generate_video
// Backend returns jobId immediately
// MCP host polls get_job until ready

const pollJob = async (jobId) => {
  let attempts = 0;
  while (attempts < 120) { // 10 min timeout
    const result = await callBackend("get_job", { jobId });
    
    if (result.status === "succeeded") {
      return result.outputUrl;
    }
    if (result.status === "failed") {
      throw new Error(`Render failed: ${result.error}`);
    }
    
    // Still rendering — wait 5 seconds
    await sleep(5000);
    attempts++;
  }
  
  throw new Error("Timeout waiting for render");
};
```

### Pattern 2: Fallback to Default Schema

```typescript
async function loadToolSchemas() {
  try {
    return await fetchFromBackend();
  } catch (error) {
    console.warn("Could not fetch live schemas, using bundled defaults");
    return BUNDLED_SCHEMAS;
  }
}
```

### Pattern 3: Rate Limit Retry

```typescript
const sleep = (ms) => new Promise(resolve => setTimeout(resolve, ms));

async function callWithBackoff(fn, maxAttempts = 3) {
  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (error.status === 429 && attempt < maxAttempts) {
        const delay = Math.pow(2, attempt - 1) * 1000;
        console.log(`Rate limited, retrying in ${delay}ms...`);
        await sleep(delay);
      } else {
        throw error;
      }
    }
  }
}
```

### Pattern 4: Tool Input Validation

```typescript
function validateVideoInput(args) {
  const errors = [];
  
  if (!args.prompt || typeof args.prompt !== "string") {
    errors.push("prompt is required and must be a string");
  }
  if (args.prompt.length > 2000) {
    errors.push("prompt must be <= 2000 characters");
  }
  if (args.durationSec && (args.durationSec < 1 || args.durationSec > 60)) {
    errors.push("durationSec must be between 1 and 60");
  }
  
  if (errors.length > 0) {
    throw new Error(`Invalid input: ${errors.join("; ")}`);
  }
}
```

---

## Summary

| Component | Pattern | Status |
|-----------|---------|--------|
| Tool discovery | Fetch schema from backend | Production |
| Authentication | Bearer token in Authorization header | Production |
| Error handling | Exponential backoff + meaningful errors | Production |
| Self-update | Fetch `/api/mcp/schema` on startup | Production |
| Testing | Unit + integration + smoke tests | Production |
| Deployment | npm publish + env var config | Production |

This architecture ensures:
- ✅ Tools stay in sync without server restart
- ✅ Auth is secure and stateless
- ✅ Errors are recoverable and informative
- ✅ The server is testable and deployable
- ✅ New features roll out automatically to all agents

---

**Next:** Read the [API Reference](./agent-guide.md) for all 35 available tools.
