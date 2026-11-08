# Google Drive document discovery for AI assistants

Find the right document by its live metadata without building a custom Drive client or downloading file contents.

[Start locally](#get-started) · [Connect your assistant](#connect-your-assistant) · [Available tools](#available-tools) · [Hosted PipMCP setup](#use-pipmcp-for-production)

## Built for useful workflows

- **Find a relevant document.** List accessible file metadata with the documented query fields.
- **Check a selected file.** Inspect the metadata of a chosen file ID before using it in another workflow.
- **Give an assistant document context.** Expose the same metadata tools through stdio MCP; this package does not download or read file contents.

## Why use this package?

Get a focused Google Drive MCP integration without assembling request mappings and a stdio transport yourself. The package provides documented input schemas, provider authentication handling and an example CLI, with the same tools available to your assistant.

The selected tools keep this Google Drive workflow read-only.

## Get started

**Requirements:** Node.js 22 or newer, npm, and access to the provider account you intend to use.

```sh
git clone https://github.com/jannikco/google-drive-mcp.git
cd google-drive-mcp
npm ci --ignore-scripts
npm test
npm start
```

`npm start` shows the tool inventory and workflow offline. It does not authenticate or call the provider. The tests use provider fixtures and stdio transport; they do not modify your account.

### Configure your provider account

The current release connects directly to the provider with **your own credentials**. It does not require a PipMCP key for local execution. Configure the environment of the process or MCP client that runs the server; `.env` files are not loaded automatically.

| Environment variable | What to supply |
| --- | --- |
| `GOOGLE_DRIVE_ACCESS_TOKEN` | Your provider OAuth access token; use the documented login helper where supported. |

Enable Google Drive API and create your own Desktop OAuth client. Set GOOGLE_DRIVE_CLIENT_ID; optionally set GOOGLE_DRIVE_CLIENT_SECRET if supplied by your client configuration. Native loopback callback is http://127.0.0.1:42813/callback. Consent scopes cover only file metadata reads. Enable your own app consent/test users as required. No PipMCP account is needed.

[Provider credential/reference documentation](https://www.googleapis.com/discovery/v1/apis/drive/v3/rest). Keep credentials out of commits, issue reports and shared client configuration.

### Optional local OAuth helper

Use your own provider OAuth application. Configure `GOOGLE_DRIVE_CLIENT_ID`, `GOOGLE_DRIVE_CLIENT_SECRET`, `GOOGLE_DRIVE_OAUTH_SCOPES` as described in connector.json and the provider reference. For refreshes, the server process must also have the required application configuration. A manually supplied access token remains an alternative.

```sh
node local-auth/cli.mjs login ./connector.json
node local-auth/cli.mjs status ./connector.json
```

The helper stores credentials in the native OS credential store where supported. Login does not establish that every tool or permission works. See [local auth documentation](./local-auth/README.md).

### Run your first provider read

After configuring the environment, run `list_files`:

```sh
npm start -- --call list_files '{"pageSize":10,"fields":"nextPageToken,files(id,name,mimeType)"}'
```

Replace sample account, record, file and project IDs with values from your authorized account. This command makes a real provider request and returns its result for you to inspect.

## Available tools

| Capability | MCP tool | Effect | Required inputs |
| --- | --- | --- | --- |
| List file metadata accessible to the authorized user. | `list_files` | Read | None |
| Read a chosen file’s metadata; does not download file contents. | `get_file_metadata` | Read | `fileId` |

Tool schemas and bindings are in [connector.json](./connector.json). These are the selected operations this package ships.

### A practical tool sequence

1. Use `list_files` — List file metadata accessible to the authorized user.
2. Use `get_file_metadata` — Read a chosen file’s metadata; does not download file contents.

## Connect your assistant

Add this stdio server to an MCP-compatible client. Replace the absolute path and credential placeholders. Use your client’s secret/environment controls instead of committing this configuration.

```json
{
  "mcpServers": {
    "google-drive-mcp": {
      "command": "node",
      "args": [
        "/absolute/path/google-drive-mcp/server.mjs"
      ],
      "env": {
        "GOOGLE_DRIVE_ACCESS_TOKEN": "YOUR_GOOGLE_DRIVE_ACCESS_TOKEN"
      }
    }
  }
}
```

`node server.mjs` starts the MCP server; `npm start` runs the example CLI. The server reserves stdout for MCP messages. If the client does not inherit your shell environment, pass the required variables explicitly.

## Read-only scope

The shipped Google Drive tools are read-only. This package does not provide a write command or silently expand the provider permissions.

## Use PipMCP for production

The local package is useful when you want to run these selected Google Drive operations in your own process. PipMCP provides a hosted place to configure provider connections, publish tools, issue endpoint access tokens, apply server-side policy and approvals, and inspect execution/usage records. Provider credentials and hosted execution are configured in that service.

**[Set up Google Drive in PipMCP](https://pipmcp.com/github/start?app=google-drive&integration=google-drive-mcp&utm_source=github&utm_campaign=github-google-drive-mcp&utm_content=readme)**

A managed catalog template is listed, but that is not evidence that its tools match this repository or that your account is connected. Complete provider setup and inspect the endpoint’s actual tool inventory and permissions before use.

Local credentials are not uploaded by this repository. A hosted endpoint uses its own PipMCP endpoint access token and published tool inventory; the local and hosted credentials are separate.

## Scope and validation

- The installed package has HTTP request fixtures and MCP stdio `tools/call` tests.
- Fixture success checks client behavior; it does not establish provider authentication, paid-plan access or live account execution.
- No live provider execution is claimed for this package by these tests.
- Hosted tool names, schemas, permissions and availability can differ from this local package. A queued or accepted job/message is not proof of completion or delivery. Provider API plans, account rules and limits apply.
- See [integration.json](./integration.json) for operation-scoped evidence and the provider-specific limits above.

## FAQ

**Do I need a Google Drive account?**

You need the provider account, token and permissions required by these operations. Provider charges and API limits remain with that account.

**Does this version require a PipMCP API key?**

This package uses your provider credentials for local execution. To use the hosted service, configure the provider connection in PipMCP and use the endpoint access token issued there.

**Why is a tool missing or a request rejected?**

This repository exposes the selected tools above. Check the required environment variables, provider scopes, account/region requirements and input IDs first. Hosted endpoints have their own published inventory and may exclude tools by role or policy. Do not retry a failed write without checking its outcome.

**Is this an official provider SDK or a hosted-service guarantee?**

This is an independently maintained MCP integration. Its fixtures and source references do not imply provider endorsement, unrestricted API access, or identical hosted behavior.

## Guides and related workflows

- [Google Drive integration guide](https://jannikco.github.io/pipmcp-connectors/integrations/google-drive/)
- [Google Drive workflow and tool sequence](https://jannikco.github.io/pipmcp-connectors/workflows/google-drive/)
- [More integration workflows](https://jannikco.github.io/pipmcp-connectors/workflows/)
- [Typed LeadAdapter API client](https://github.com/jannikco/linkedin-typescript-sdk)
- [LeadAdapter Python API client](https://github.com/jannikco/linkedin-python-sdk)
- [LinkedIn API quickstart](https://github.com/jannikco/linkedin-api)
- [LinkedIn people and post search](https://github.com/jannikco/linkedin-search-api)
- [LinkedIn post engagers](https://github.com/jannikco/linkedin-post-engagers)
- [LeadAdapter LinkedIn MCP server](https://github.com/jannikco/linkedin-mcp)
- [OpenAPI to MCP import starter](https://github.com/jannikco/openapi-to-mcp)
- [Swagger to MCP import starter](https://github.com/jannikco/swagger-to-mcp)
- [Google Drive API and credential reference](https://www.googleapis.com/discovery/v1/apis/drive/v3/rest)
- [Additional official source 2](https://developers.google.com/workspace/drive/api/reference/rest/v3/files)
- [Additional official source 3](https://developers.google.com/identity/protocols/oauth2/native-app)
- [Additional official source 4](https://developers.google.com/workspace/drive/api/reference/rest/v3/files/list)

Maintained by [jannikco](https://github.com/jannikco). MIT for this repository’s code; dependencies retain their licenses. [Report a reproducible issue](https://github.com/jannikco/google-drive-mcp/issues) with redacted input and error details. For credentials or security issues, use [private contact](https://pipmcp.com/contact).
