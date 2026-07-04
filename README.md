[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/amgadabdelhafez-dbx-mcp-server-badge.png)](https://mseep.ai/app/amgadabdelhafez-dbx-mcp-server)

# dbx-mcp-server

A Model Context Protocol (MCP) server that provides integration with Dropbox, allowing MCP-compatible clients to interact with Dropbox through a set of powerful tools.

**Important Disclaimer:** This project is not affiliated with, endorsed by, or sponsored by Dropbox. It is an independent integration that works with Dropbox's public API.

## Table of Contents

- [Quick Start](#quick-start)
- [Installation](#installation)
- [Authentication](#authentication)
- [Available Tools](#available-tools)
- [Required Dropbox Permissions](#required-dropbox-permissions)
- [Usage Examples](#usage-examples)
- [Development](#development)
- [License](#license)

## Quick Start

1. Clone the repository
2. Run `npm install` to install dependencies
3. Run `npm run build` to build the project
4. Run `npm run setup`
5. Configure your MCP client to use the server

## Prerequisites

Register a Dropbox app at [Dropbox App Console](https://www.dropbox.com/developers/apps):

- Choose "Scoped access" API
- Choose the access type your app needs
- Name your app and click "Create app"
- Under "Permissions", select the desired permissions for the actions you will be using, for example:
    - `files.metadata.read`
    - `files.content.read`
    - `files.content.write`
    - `sharing.write`
    - `account_info.read`
- Add `http://localhost` as your redirect URI
- Note your App key and App secret

## Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/your-username/dbx-mcp-server.git
   cd dbx-mcp-server
   ```

2. **Install dependencies and build**

   ```bash
   npm install
   npm run build
   ```

3. **Run the setup script**

   ```bash
   npm run setup
   ```

4. **Add to MCP settings**

   Add the following to your MCP settings file:

   ```json
   {
     "mcpServers": {
       "dbx": {
         "command": "node",
         "args": ["/path/to/dbx-mcp-server/build/index.js"]
       }
     }
   }
   ```

## Authentication

The server uses OAuth 2.0 with PKCE for secure authentication with Dropbox.

### Environment Variables

Required:

- `DROPBOX_APP_KEY`: Your Dropbox app's key
- `DROPBOX_APP_SECRET`: Your Dropbox app's secret
- `DROPBOX_REDIRECT_URI`: OAuth redirect URI
- `TOKEN_ENCRYPTION_KEY`: 32+ character key for token encryption

Optional:

- `TOKEN_REFRESH_THRESHOLD_MINUTES`: Minutes before expiration to refresh token (default: 5)
- `MAX_TOKEN_REFRESH_RETRIES`: Maximum number of refresh attempts (default: 3)
- `TOKEN_REFRESH_RETRY_DELAY_MS`: Delay between refresh attempts in ms (default: 1000)

## Available Tools

### File Operations

- `list_files`: List files in a directory
- `upload_file`: Upload a file
- `download_file`: Download a file
- `safe_delete_item`: Safely delete with recycle bin support
- `create_folder`: Create a new folder
- `copy_item`: Copy a file or folder
- `move_item`: Move or rename a file/folder

### Metadata and Search

- `get_file_metadata`: Get file/folder metadata
- `search_file_db`: Search files and folders
- `get_sharing_link`: Create sharing links
- `get_file_content`: Get file contents

### Account Operations

- `get_account_info`: Get account information

## Required Dropbox Permissions

Below is a mapping of server actions to the minimum Dropbox OAuth scopes (permissions) required:

| Server Action         | Required Dropbox Scopes                        |
|---------------------- |-----------------------------------------------|
| list_files            | `files.metadata.read`                         |
| upload_file           | `files.content.write`, `files.metadata.write` |
| download_file         | `files.content.read`                          |
| safe_delete_item      | `files.metadata.write`                        |
| create_folder         | `files.metadata.write`                        |
| copy_item             | `files.content.write`, `files.metadata.write` |
| move_item             | `files.content.write`, `files.metadata.write` |
| get_file_metadata     | `files.metadata.read`                         |
| search_file_db        | `files.metadata.read`                         |
| get_sharing_link      | `sharing.write`                               |
| get_file_content      | `files.content.read`                          |
| get_account_info      | `account_info.read`                           |

**Additional Scopes (if needed):**

- `sharing.read`: View sharing settings and collaborators
- `file_requests.read` / `file_requests.write`: For file request features
- `contacts.read` / `contacts.write`: For accessing Dropbox contacts

For more details on Dropbox scopes, see the [Dropbox Permissions Documentation](https://www.dropbox.com/developers/reference/oauth-guide#scopes).

## Usage Examples

```typescript
// List files in root directory
await mcp.useTool("dbx-mcp-server", "list_files", { path: "" });

// Upload a file
await mcp.useTool("dbx-mcp-server", "upload_file", {
  path: "/test.txt",
  content: Buffer.from("Hello World").toString("base64"),
});

// Search for files
await mcp.useTool("dbx-mcp-server", "search_file_db", {
  query: "report",
  path: "/Documents",
  max_results: 10,
});
```

## Development

Built with:

- TypeScript
- Model Context Protocol SDK
- Dropbox SDK v10.34.0
- Dropbox API v2

## License

MIT License

Copyright (c) 2025 MCP Server Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
