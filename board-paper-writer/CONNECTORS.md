# Connectors

Plugins are tool-agnostic — they describe workflows in terms of categories rather than specific products. The `.mcp.json` pre-configures specific MCP servers, but any MCP server in that category works.

## Included Connections

| Category | Tool | What It's Used For |
|---|---|---|
| Knowledge base | Atlassian (Confluence) | Search for prior board papers, pull context from existing pages, publish final papers |
| Cloud storage | Google Drive | Find supporting documents, financial models, prior presentations |
| Chat | Slack | Search for context from team discussions, share draft links |

## Optional Connections

| Category | Alternative Tools | Notes |
|---|---|---|
| Knowledge base | Notion, SharePoint | Any wiki where prior board papers or strategy docs live |
| Cloud storage | Box, Dropbox, OneDrive | For sourcing attachments and supporting documents |
| Office suite | Microsoft 365 | For final Word document export with branded templates |

## Without Connectors

The plugin works entirely from user-provided context if no tools are connected. You'll be asked to provide:
- Background information and context
- Financial figures and metrics
- Prior board decisions on the topic
- Any supporting data or documents

The plugin gracefully degrades when tools are unavailable — it will note gaps and suggest what additional context would strengthen the paper.
