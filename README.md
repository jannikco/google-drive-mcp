# Google Drive MCP server

Search available file metadata and inspect a chosen document before requesting its contents.

A direct-provider MCP server with useful local execution. No PipMCP account is required for local use. Provider API access and any provider charges are your responsibility.

## Quick start

Requires Node.js22+. Clone this repository, then run:

```sh
npm ci --ignore-scripts
npm test
npm start
```

The default command inspects the tools offline. Configure your own credentials in your environment:

- `GOOGLE_DRIVE_ACCESS_TOKEN`

For local OAuth, use node local-auth/cli.mjs login ./connector.json with your own provider app; see AUTH.md. Your tokens remain in the native OS credential store.

Credential help: https://www.googleapis.com/discovery/v1/apis/drive/v3/rest

## Execute a real read

Replace example record IDs with records you are authorized to access.

```sh
npm start -- --call list_files '{"pageSize":10,"fields":"nextPageToken,files(id,name,mimeType)"}'
```

## Use with an MCP client

Launch `node /absolute/path/google-drive-mcp/server.mjs` using stdio transport; pass credentials through the client environment. Stdout is reserved for JSON-RPC.

## Tools

| Tool | Effect | Purpose |
| --- | --- | --- |
| list_files | Read | List file metadata accessible to the authorized user. |
| get_file_metadata | Read | Read a chosen file’s metadata; does not download file contents. |

## Concrete workflow

Search available file metadata and inspect a chosen document before requesting its contents.

1. list_files
2. get_file_metadata

## Optional writes

Writes are disabled by default. Set CONNECTOR_ALLOW_WRITES=true and supply `_approval: {approved:true,idempotencyKey:"unique-request-id"}` on the specific call. Do not reuse a key for a different request. Uncertain outcomes are never automatically replayed; inspect the provider before another attempt.

## Capability evidence

Local: **Tested — HTTP fixtures and real stdio tools/call**. Managed: **Documented**. Provider authentication and live operations have not been established by these fixture tests. Evidence applies per operation and execution mode; setup discovery does not imply provider execution.

## Managed production

[Activate with PipMCP](https://pipmcp.com/github/start?app=google-drive&integration=google-drive-mcp&utm_source=github&utm_campaign=github-google-drive-mcp&utm_content=readme) when you want the hosted service.  Local credentials are not uploaded by this code. Review provider permissions in the managed onboarding. [Directory and related projects](https://jannikco.github.io/pipmcp-connectors/integrations/google-drive/).

## Provenance and maintenance

- https://developers.google.com/workspace/drive/api/reference/rest/v3/files
- https://developers.google.com/identity/protocols/oauth2/native-app
- https://developers.google.com/workspace/drive/api/reference/rest/v3/files/list

Maintainer:jannikco. MIT license for this code. See connector.json for selected operations and integration.json for evidence. Runtime dependencies retain their own licenses. Report sensitive issues through https://pipmcp.com/contact; do not post credentials or customer data in public issues.
