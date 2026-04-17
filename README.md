# MCP Registry

This project provides Zoom's registry of MCP servers at `registry.modelcontextprotocol.io`. It contains the published registry metadata for remote MCP servers. It does not contain the server implementations themselves.

## Published Servers

### `io.github.zoom/zoom-workspace`

- Registry metadata: [zoom-workspace/server.json](./zoom-workspace/server.json)
- Remote URL: `https://mcp.zoom.us/mcp/zoom/streamable`

Zoom Workspace exposes Zoom meetings, Team Chat, and Zoom Docs to MCP clients. It supports natural-language retrieval of AI-generated summaries, transcripts, recordings, shared documents, and meeting assets, along with recording-specific resources such as playback links and next steps. It can retrieve Zoom Docs content, list cloud recordings, and create follow-up Zoom Docs from Markdown. This server is intended for workflows such as locating where a topic was discussed, generating agendas, capturing notes, identifying action items, and creating follow-up documents from existing Zoom collaboration data.

### `io.github.zoom/zoom-docs`

- Registry metadata: [zoom-docs/server.json](./zoom-docs/server.json)
- Remote URL: `https://mcp.zoom.us/mcp/docs/streamable`

Zoom Docs provides a focused MCP surface for working with Zoom documents and notes. It currently supports creating new Zoom Docs from Markdown content and retrieving existing Zoom Docs or notes in Markdown form. The server is designed for structured document generation and readback flows used by AI agents and developer tooling, without exposing broader document lifecycle operations such as update or delete.

### `io.github.zoom/zoom-whiteboard`

- Registry metadata: [zoom-whiteboard/server.json](./zoom-whiteboard/server.json)
- Remote URL: `https://mcp.zoom.us/mcp/whiteboard/streamable`

Zoom Whiteboard provides MCP tools for creating new editable whiteboards and diagrams from prompts and for locating existing boards in a user's account. It is intended for workshop planning, visual collaboration, and diagram-generation use cases, including simple flowcharts and more technical diagram types. The server only exposes boards the authenticated user can access.

## Registry Notes

- Namespace: `io.github.zoom/*`
- Transport: `streamable-http`
- Current metadata version in this repository: `1.0.1` for all three published entries

## Published Registry API Records

- Search listing: `https://registry.modelcontextprotocol.io/v0.1/servers?search=zoom&limit=100`
- Workspace latest version: `https://registry.modelcontextprotocol.io/v0.1/servers/io.github.zoom%2Fzoom-workspace/versions/latest`
- Docs latest version: `https://registry.modelcontextprotocol.io/v0.1/servers/io.github.zoom%2Fzoom-docs/versions/latest`
- Whiteboard latest version: `https://registry.modelcontextprotocol.io/v0.1/servers/io.github.zoom%2Fzoom-whiteboard/versions/latest`

## Publishing Workflow

The MCP Registry entries in this repository are published with `mcp-publisher`.

For the Zoom-specific step-by-step publishing history and lessons learned, see [RUNBOOK.md](./RUNBOOK.md).

### Prerequisites

- Install `mcp-publisher`
- Authenticate with GitHub using an account that has access to the target GitHub organization
- Ensure the GitHub organization membership associated with the namespace is public

### Publish a New Version

1. Update the relevant `server.json`.
2. Increment the `version` field before publishing an update.
3. Change into the corresponding server directory.
4. Refresh or establish a login session if needed:

   ```bash
   mcp-publisher login github
   ```

5. Publish the entry:

   ```bash
   mcp-publisher publish
   ```

6. Verify the published record using the registry API URLs listed above.

Example:

```bash
cd zoom-workspace
mcp-publisher publish
```

## Maintenance

Published entries can be updated by modifying the corresponding `server.json`, incrementing the version, and republishing from that server's directory with `mcp-publisher publish`.
