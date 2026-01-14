# FastMCP Cloud Deployment Guide

This document describes the changes made to support FastMCP Cloud deployment and the required configuration.

## Changes Made

1. **Created `fastmcp.json`**: Configuration file required by FastMCP Cloud that specifies:
   - Source path and entrypoint
   - Python version and dependencies
   - Deployment settings (transport, host, port, path)

2. **Refactored `server.py`**:
   - Added `create_args_from_env()` function to build arguments from environment variables
   - Added `create_server()` function that can work with either CLI args or environment variables
   - Created module-level `server` instance for FastMCP Cloud to discover
   - Maintained backward compatibility with CLI usage

## Required Environment Variables

For FastMCP Cloud deployment, you must set the following environment variables in the FastMCP Cloud interface:

### Required
- `SERVICE_CONFIG_FILE`: Path to your service configuration YAML file (e.g., `/app/services/configuration.yaml`)

### Snowflake Connection (at least one authentication method)

**Option 1: Username/Password**
- `SNOWFLAKE_ACCOUNT`: Your Snowflake account identifier
- `SNOWFLAKE_USER`: Your Snowflake username
- `SNOWFLAKE_PASSWORD`: Your password or Programmatic Access Token

**Option 2: Key Pair Authentication**
- `SNOWFLAKE_ACCOUNT`: Your Snowflake account identifier
- `SNOWFLAKE_USER`: Your Snowflake username
- `SNOWFLAKE_PRIVATE_KEY`: Your private key
- `SNOWFLAKE_PRIVATE_KEY_FILE_PWD`: Password for encrypted private key (if applicable)

**Option 3: Connection Name**
- Use Snowflake's connection configuration file with `--connection-name` (requires additional setup)

### Optional Connection Parameters
- `SNOWFLAKE_HOST`: Snowflake host URL (if different from default)
- `SNOWFLAKE_ROLE`: Role to use for connection
- `SNOWFLAKE_WAREHOUSE`: Warehouse to use for queries
- `SNOWFLAKE_PASSCODE`: MFA passcode (if using MFA)

### Server Configuration (Optional - defaults provided)
- `SNOWFLAKE_MCP_TRANSPORT`: Transport type (default: "http")
- `SNOWFLAKE_MCP_HOST`: Host address (default: "0.0.0.0")
- `SNOWFLAKE_MCP_PORT`: Port number (default: 8000)
- `SNOWFLAKE_MCP_ENDPOINT`: Endpoint path (default: "/mcp")
- `SNOWFLAKE_MCP_VERBOSE`: Enable verbose logging ("true"/"false", default: "false")

## Deployment Steps

1. **Ensure `fastmcp.json` is in the repository root**
   - The file has been created with the correct configuration

2. **Set Environment Variables in FastMCP Cloud**
   - Go to your project settings in FastMCP Cloud
   - Add all required environment variables listed above
   - Make sure `SERVICE_CONFIG_FILE` points to a valid path in your deployment

3. **Deploy**
   - FastMCP Cloud will automatically detect `fastmcp.json` and deploy your server
   - The server will be accessible at `https://your-project.fastmcp.app/mcp`

## Troubleshooting

### Server not initializing
- Check that `SERVICE_CONFIG_FILE` is set and points to a valid file
- Verify all required Snowflake connection environment variables are set
- Check logs in FastMCP Cloud dashboard for detailed error messages

### Connection errors
- Verify your Snowflake credentials are correct
- Ensure your Snowflake account allows connections from FastMCP Cloud's IP addresses
- Check that the specified role has necessary permissions

### Configuration file not found
- Ensure `SERVICE_CONFIG_FILE` path is correct for your deployment environment
- The path should be absolute or relative to the working directory
- Consider mounting the config file as a volume if using containers

## Notes

- The module-level server instance is only initialized when `SERVICE_CONFIG_FILE` is set
- This ensures backward compatibility with CLI usage where arguments are parsed from command line
- FastMCP Cloud will automatically discover and use the module-level `server` instance
