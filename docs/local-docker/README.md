# Local Docker Stack

This stack runs the local services Claude Context needs for semantic code
search:

- Milvus standalone on `127.0.0.1:19530`
- Milvus Web UI on `http://127.0.0.1:9091/webui/`
- Optional Ollama on `127.0.0.1:11434`

The Claude Context MCP server is intentionally not containerized here. MCP runs
over stdio and needs direct access to your project folders, so running it on the
host or in WSL keeps paths simple.

## Start Milvus

From the repository root:

```bash
docker compose up -d
docker compose ps
```

This starts `etcd`, `minio`, and `milvus`. Ports are bound to `127.0.0.1` only,
so the stack is local to your machine.

## Optional: Start Ollama in Docker

Use this only if you do not want to use your existing host Ollama.

```bash
docker compose --profile ollama up -d
```

By default the one-shot `ollama-pull` service pulls
`mxbai-embed-large:latest`. To use another model:

```bash
CLAUDE_CONTEXT_OLLAMA_MODEL=nomic-embed-text:latest docker compose --profile ollama up -d
```

In PowerShell:

```powershell
$env:CLAUDE_CONTEXT_OLLAMA_MODEL = "nomic-embed-text:latest"
docker compose --profile ollama up -d
```

## Configure Claude Context

If your MCP server runs from WSL:

```bash
mkdir -p ~/.context
cp docker/claude-context.env.example ~/.context/.env
```

If your MCP server runs directly on Windows, place the same file at:

```text
C:\Users\YvesT\.context\.env
```

With the default example config, Claude Context will use:

```text
OLLAMA_HOST=http://127.0.0.1:11434
MILVUS_ADDRESS=http://127.0.0.1:19530
```

## Use Another Project

Start the MCP server locally, then index a project by absolute path, for
example:

```text
/mnt/c/Users/YvesT/Documents/GitHub/my-other-project
```

On Windows-native clients, use the Windows path instead:

```text
C:\Users\YvesT\Documents\GitHub\my-other-project
```

## Stop Or Reset

Stop containers and keep data:

```bash
docker compose down
```

Delete all indexed data and models stored by this stack:

```bash
docker compose down -v
```
