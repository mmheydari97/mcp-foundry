## 🧠 Part 1: Azure AI Agent + Knowledge Base Setup

Create and configure **Agent**, **Knowledge Base**, and **Tools** through the UI, so it will retrieve context from an Azure service.

Before Deployment make sure your agent has the **retrieval capability** tied to your KB.

---

## 🧱 Part 2: Clone MCP Foundry Template

We'll use the official Azure MCP Foundry server template:
```bash
git clone https://github.com/azure-ai-foundry/mcp-foundry.git
cd mcp-foundry/src/python
```

---

## 🧪 Part 3: Install Python Dependencies

```bash
uv venv .venv python=python3.12
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
pip install -e .[dev]
```

---

## 🛠️ Part 4: Configure MCP Server to Use Your Agent

Create a **`.env`** file with your Azure AI project details:

```env
PROJECT_CONNECTION_STRING=your_project_connection_string
DEFAULT_AGENT_ID=your_agent_id_with_kb
```

> These come from your Azure AI Project → Overview → Connection string and agent settings.

---

## 📜 Part 5: Code to Route Queries to Azure Agent

`azure_agent_mcp_server.py` (already exists in the repo) will **automatically**:

- Parse MCP messages
- Call the Azure agent via your `PROJECT_CONNECTION_STRING`
- Return streaming responses via SSE

You don’t need to modify anything unless you want to customize behavior. If so, edit `src/python/azure_agent_mcp_server.py`.

---

## 🚀 Part 6: Deploy as Azure Container App

### Step 1: Install Azure Dev CLI
```bash
winget install microsoft.azd
azd auth login
```

### Step 2: Create Infra (first time only)
```bash
azd init --environment mcp-kb-server
azd up # Docker daemon should be running
```

Follow prompts to choose:
- Subscription
- Region
- Project name (e.g. `mcp-kb-server`)

This provisions:
- Azure Container App
- Storage
- Networking
- Logging

It also builds and deploys the MCP server from the current code.

---

## 🌐 Part 7: Test Your MCP Server

After deployment, you’ll get a URL like:
```
https://python.[].eastus2.azurecontainerapps.io/
```

Test it locally (optional):
```bash
curl https://python.[].eastus2.azurecontainerapps.io//sse -N \
  -H "Accept: text/event-stream" \
  -H "x-api-key: mysecret" \
  -d '{"query": "What’s the capital of Canada?"}'
```

---

## 🖥️ Part 8: Connect MCP to Claude Desktop

Edit your Claude Desktop config file:

### `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "azure-kb-agent": {
      "type": "sse",
      "url": "https://python.[].eastus2.azurecontainerapps.io//sse",
      "headers": {
        "x-api-key": "your_api_key"
      }
    }
  }
}
```