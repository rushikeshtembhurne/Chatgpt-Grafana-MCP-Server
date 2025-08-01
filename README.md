# Chatgpt-Grafana-MCP-Server
Chatgpt-Grafana-MCP-Server

# MCP SuperAssistant Setup with Grafana, Prometheus, and Loki

This guide explains how to set up **Grafana**, **Prometheus**, and **Loki** as MCP (Model Context Protocol) servers and connect them with **ChatGPT** using the [MCP SuperAssistant Proxy](https://github.com/srbhptl39/MCP-SuperAssistant).

---

## 📌 Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop) installed and running  
- [Node.js](https://nodejs.org/) (LTS version recommended)  
- ChatGPT Desktop with MCP server extension installed  
- Access to GitHub (for MCP SuperAssistant)

---

## 🚀 Step 1: Run Grafana

```bash
docker run -d -p 3000:3000 --name=grafana grafana/grafana
```

•	Login at http://localhost:3000
•	Default credentials:
•	Username: admin
•	Password: admin

Create an API Key in Grafana
	1.	Go to Configuration → API Keys
	2.	Create a new key with Admin role
	3.	Save the generated API key for later use
 
🚀 Step 2: Run Prometheus
```bash
docker run -d -p 9090:9090 --name=prometheus prom/prometheus
```
Prometheus will be available at:
👉 http://localhost:9090

🚀 Step 3: Run MCP Servers

Grafana MCP Server

```bash
docker pull mcp/grafana

docker run -d --name grafana_mcp \
  -e GRAFANA_URL=http://host.docker.internal:3000 \
  -e GRAFANA_API_KEY=<YOUR_GRAFANA_API_KEY> \
  mcp/grafana -t stdio -debug
```
Prometheus MCP Server

```bash
docker pull ghcr.io/pab1it0/prometheus-mcp-server:latest

docker run -d --name prometheus_mcp \
  -e PROMETHEUS_URL=http://host.docker.internal:9090 \
  ghcr.io/pab1it0/prometheus-mcp-server:latest
```
Loki MCP Server (Optional)

```bash
docker run -d --name loki_mcp \
  -e LOKI_URL=http://host.docker.internal:3100 \
  loki-mcp-server:latest
```
Check if containers are running:
```bash
docker ps -a
```
⚠️ MCP servers will exit if not connected to ChatGPT — this is expected.

📄 Step 4: Create ChatGPT Config File
```bash
{
  "mcpServers": {
    "grafana": {
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "-e",
        "GRAFANA_URL",
        "-e",
        "GRAFANA_API_KEY",
        "mcp/grafana",
        "-t",
        "stdio",
        "-debug"
      ],
      "env": {
        "GRAFANA_URL": "http://host.docker.internal:3000",
        "GRAFANA_API_KEY": "<YOUR_GRAFANA_API_KEY>"
      }
    },
    "prometheus": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "PROMETHEUS_URL",
        "ghcr.io/pab1it0/prometheus-mcp-server:latest"
      ],
      "env": {
        "PROMETHEUS_URL": "http://host.docker.internal:9090"
      }
    },
    "loki": {
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "-e",
        "LOKI_URL",
        "loki-mcp-server:latest"
      ],
      "env": {
        "LOKI_URL": "http://host.docker.internal:3100"
      }
    }
  }
}
```
🚀 Step 5: Add MCP SuperAssistant Proxy Chrome Extention
https://github.com/srbhptl39/MCP-SuperAssistant

Then run:
```bash
npx @srbhptl39/mcp-superassistant-proxy@latest --config ~/github/MCP_Servers/chatgpt_web_config.json
```
✅ Ensure the chatgpt_web_config.json file path is correct.
✅ Final Notes
	•	Keep your Grafana API key secret
	•	If using on Mac/Windows, host.docker.internal is correct.
For Linux, replace with your machine’s IP.
	•	Restart the MCP proxy if you update the config file.

⸻

📚 Resources
	•	Grafana Docs
	•	Prometheus Docs
	•	Loki Docs
	•	MCP SuperAssistant Proxy GitHub





 
 


