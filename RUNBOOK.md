# MCP Registry Publishing Runbook

This runbook captures the step-by-step flow used to publish Zoom remote MCP server metadata to the official MCP Registry at `registry.modelcontextprotocol.io`.

It is intended as an internal operational record for how the Zoom entries were published and updated. Official MCP Registry documentation may evolve over time, so treat this file as the Zoom-specific runbook and use the current MCP docs for any platform-level changes.

## MCP Registry Publishing Flow For Zoom Remote MCP Servers

1. Create a local repo for the registry metadata.
   We used:
   - `~/temp/mcp-registry-publish`

2. Create one folder per server, each containing a `server.json`.
   We created:
   - `zoom-workspace/server.json`
   - `zoom-docs/server.json`
   - `zoom-whiteboard/server.json`

3. Use GitHub namespace naming for the registry entries.
   Because GitHub org access was available but DNS/domain verification was not, the names used were:
   - `io.github.zoom/zoom-workspace`
   - `io.github.zoom/zoom-docs`
   - `io.github.zoom/zoom-whiteboard`

4. Use remote-only entries with `streamable-http`.
   The remote URLs used were:
   - `https://mcp.zoom.us/mcp/zoom/streamable`
   - `https://mcp.zoom.us/mcp/docs/streamable`
   - `https://mcp.zoom.us/mcp/whiteboard/streamable`

5. Install `mcp-publisher`.
   Confirmed to work on Linux.

6. Log in with `mcp-publisher`.
   If auth expired, re-run:

```bash
mcp-publisher logout
mcp-publisher login
```

7. Make GitHub organization membership public if publishing under `io.github.zoom/*`.
   This was required because the first publish attempt failed with a `403` saying only the personal namespace was permitted. Making the Zoom org membership public fixed that.

8. Publish each server from its own folder.
   Commands:

```bash
cd ~/temp/mcp-registry-publish/zoom-workspace
mcp-publisher publish

cd ~/temp/mcp-registry-publish/zoom-docs
mcp-publisher publish

cd ~/temp/mcp-registry-publish/zoom-whiteboard
mcp-publisher publish
```

9. Fix validation issues from the registry.
   The main one hit was:
   - `description` max length is 100 characters
   Two descriptions had to be shortened before publish succeeded.

10. Verify the entries through the registry API.
    Used:
    - `https://registry.modelcontextprotocol.io/v0/servers?search=zoom&limit=100`
    - `https://registry.modelcontextprotocol.io/v0/servers/io.github.zoom%2Fzoom-workspace/versions/latest`
    - `https://registry.modelcontextprotocol.io/v0/servers/io.github.zoom%2Fzoom-docs/versions/latest`
    - `https://registry.modelcontextprotocol.io/v0/servers/io.github.zoom%2Fzoom-whiteboard/versions/latest`

11. Create a GitHub repo for the metadata.
    Initial private repo created from the local repo.
    GitHub later indicated the canonical repo path was:
    - `https://github.com/zoom/mcp-registry`

12. Add repo hygiene files.
    Added:
    - `README.md`
    - `.gitignore` for `.mcpregistry_*`
    - `LICENSE` with MIT
    - `ASSET_LICENSES.md` excluding the Zoom logo and brand assets from MIT

13. Add richer registry metadata to each `server.json`.
    Added:
    - `repository`
    - `title`
    - `websiteUrl`
    - `_meta.io.modelcontextprotocol.registry/publisher-provided.documentation`
    - `_meta.io.modelcontextprotocol.registry/publisher-provided.publisher`
    - `_meta.io.modelcontextprotocol.registry/publisher-provided.keywords`
    - `_meta.io.modelcontextprotocol.registry/publisher-provided.examples`

14. Add icon metadata.
    Added:
    - `icons[].src`
    - `icons[].mimeType`
    - `icons[].sizes`

    Used:
    - `https://raw.githubusercontent.com/zoom/mcp-registry/main/zoom.png`

15. Make the GitHub repo public before relying on the icon URL.
    The icon URL only became valid after the repo was switched to public.
    After that, the raw icon URL returned:
    - `HTTP 200`
    - `Content-Type: image/png`

16. Update the metadata repo path after GitHub reported a repo move.
    Changed:
    - `repository.url` from `https://github.com/zoom/zoom-mcp-registry`
    - to `https://github.com/zoom/mcp-registry`

    Also changed icon URL from:
    - `https://raw.githubusercontent.com/zoom/zoom-mcp-registry/main/zoom.png`
    - to `https://raw.githubusercontent.com/zoom/mcp-registry/main/zoom.png`

17. Bump versions before republishing metadata changes.
    Initial registry publish was `1.0.0`.
    Metadata changes were prepared under `1.0.1`.

## Final `server.json` Shape

For each server, the final metadata included:

- `$schema`
- `name`
- `title`
- `description`
- `icons`
- `websiteUrl`
- `_meta`
- `repository`
- `version`
- `remotes`

Within `_meta.io.modelcontextprotocol.registry/publisher-provided`, the published metadata included:

- `documentation`
- `publisher`
- `keywords`
- `examples`

## Notes Learned From The Process

- OAuth details do not go in MCP Registry `server.json`.
- Privacy policy and support URLs are not common first-class registry fields.
- The most common useful optional fields are:
  - `repository`
  - `title`
  - `websiteUrl`
  - `icons`
  - `_meta.documentation`
  - `_meta.keywords`
  - `_meta.examples`
- `description` must stay within 100 characters.
- `icons` require a public HTTPS URL if you want them to work for registry consumers.
