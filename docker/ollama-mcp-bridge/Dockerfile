FROM python:3.10.15-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

RUN pip install uv

WORKDIR /app

RUN git clone --depth 1 --branch v0.9.2 https://github.com/jonigl/ollama-mcp-bridge.git .

ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_OLLAMA_MCP_BRIDGE=0.9.2

RUN uv sync

EXPOSE 8000

CMD ["uv", "run", "ollama-mcp-bridge"]
