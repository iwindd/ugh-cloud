# Minimal Hermes plugin layout

Use this as a starting point, then adapt the schema and handler to the actual capability.

    $HERMES_HOME/plugins/example-tool/
    ├── plugin.yaml
    ├── __init__.py
    ├── schemas.py
    └── tools.py

plugin.yaml:

    name: example-tool
    version: 1.0.0
    manifest_version: 2
    api_version: 1
    license: MIT
    description: Example custom tool

schemas.py:

    EXAMPLE_SCHEMA = {
        "name": "example_tool",
        "description": "Do one narrowly defined operation; state the input and side effects.",
        "parameters": {
            "type": "object",
            "properties": {
                "value": {"type": "string", "description": "The input value"}
            },
            "required": ["value"]
        }
    }

tools.py:

    import json

    def example_tool(args, **kwargs):
        try:
            return json.dumps({"result": args.get("value", "")})
        except Exception as exc:
            return json.dumps({"error": str(exc)})

__init__.py:

    from . import schemas, tools

    def register(ctx):
        ctx.register_tool(
            name="example_tool",
            toolset="example_tool",
            schema=schemas.EXAMPLE_SCHEMA,
            handler=tools.example_tool,
        )

Verification:

    hermes plugins doctor "$HERMES_HOME/plugins/example-tool" --ci
    hermes plugins list

For a slash command, register it from `register(ctx)` with `ctx.register_command(...)`. For async handlers, use the supported async registration flag/API from the current docs rather than calling `asyncio.run()` inside a handler. For MCP, use a separate server configuration under `mcp_servers` and verify the discovered name, normally `mcp_<server>_<tool>`.
