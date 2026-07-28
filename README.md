# Microsoft SQL Server MCP Server

[![PyPI](https://img.shields.io/pypi/v/microsoft_sql_server_mcp)](https://pypi.org/project/microsoft_sql_server_mcp/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

<a href="https://glama.ai/mcp/servers/29cpe19k30">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/29cpe19k30/badge" alt="Microsoft SQL Server MCP server" />
</a>

A Model Context Protocol (MCP) server for secure SQL Server database access.

## Features

- 🔍 List database tables
- 📊 Execute SQL queries (SELECT, INSERT, UPDATE, DELETE)
- 🔐 Multiple authentication methods (SQL, Windows, Azure AD)
- 🏢 LocalDB and Azure SQL support
- 🔌 Custom port configuration
- 🧩 Opt-in JSON and standards-compliant CSV query results
- 📦 Optional file-backed storage and pagination for oversized results

## Quick Start

### Install with an MCP client

Add the server to your MCP client configuration:

```json
{
  "mcpServers": {
    "mssql": {
      "command": "uvx",
      "args": ["microsoft_sql_server_mcp"],
      "env": {
        "MSSQL_SERVER": "localhost",
        "MSSQL_DATABASE": "your_database",
        "MSSQL_USER": "your_username",
        "MSSQL_PASSWORD": "your_password"
      }
    }
  }
}
```

## Configuration

### Basic SQL Authentication
```bash
MSSQL_SERVER=localhost          # Required
MSSQL_DATABASE=your_database    # Required
MSSQL_USER=your_username        # Required for SQL auth
MSSQL_PASSWORD=your_password    # Required for SQL auth
```

### Windows Authentication
```bash
MSSQL_SERVER=localhost
MSSQL_DATABASE=your_database
MSSQL_WINDOWS_AUTH=true         # Use Windows credentials
```

### Azure SQL Database
```bash
MSSQL_SERVER=your-server.database.windows.net
MSSQL_DATABASE=your_database
MSSQL_USER=your_username
MSSQL_PASSWORD=your_password
# Encryption is automatic for Azure
```

### Optional Settings
```bash
MSSQL_PORT=1433                 # Custom port (default: 1433)
MSSQL_ENCRYPT=true              # Force encryption
```

## Query Result Formats
The default remains `legacy`. Set `MSSQL_RESULT_FORMAT` to `json` or `csv`, or
override one `execute_sql` call with its `result_format` argument. JSON uses
compact `columns` and `rows` arrays; `NULL` is `null` and Unicode and delimiters
are preserved. `legacy` remains available for existing clients.

```bash
MSSQL_RESULT_FORMAT=json        # legacy, json, or csv
```

### Oversized Results
File-backed storage is opt-in via `MSSQL_RESULT_OUTPUT_DIR`. Results exceeding the
inline row or byte threshold are written as complete JSON files; MCP receives
metadata and preview rows instead of the full result.
```bash
MSSQL_RESULT_OUTPUT_DIR=/private/mssql-results
MSSQL_RESULT_MAX_INLINE_ROWS=200
MSSQL_RESULT_MAX_INLINE_BYTES=32768
```
Set `store_result: true` to force storage. Stored results can be read with
`get_sql_result` (bounded row pages) or `read_sql_result_chunk` (bounded file chunks).
## Export Stored Results
`export-sql-result` validates a complete canonical result and writes compact JSON to stdout.
Use either `--result-id ID [--result-dir DIR]` or `--results-json PATH`.
```bash
export-sql-result --result-id ID --result-dir /private/mssql-results > full-result.json
```

## Alternative Installation Methods

### Using pip
```bash
pip install microsoft_sql_server_mcp
```

Then in your MCP client configuration:
```json
{
  "mcpServers": {
    "mssql": {
      "command": "python",
      "args": ["-m", "mssql_mcp_server"],
      "env": { ... }
    }
  }
}
```

### Development
```bash
git clone https://github.com/RichardHan/mssql_mcp_server.git
cd mssql_mcp_server
pip install -e .
```

## Security

- Create a dedicated SQL user with minimal permissions
- Never use admin/sa accounts
- Use Windows Authentication when possible
- Enable encryption for sensitive data

## License

MIT
