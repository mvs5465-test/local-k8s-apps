# build verification touch 2026-03-08T16:44:27Z
FROM python:3.10.15-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

RUN pip install uv

WORKDIR /app

RUN git clone --depth 1 --branch v0.9.2 https://github.com/jonigl/ollama-mcp-bridge.git .

ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_OLLAMA_MCP_BRIDGE=0.9.2

RUN uv sync

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

EXPOSE 8000

ENTRYPOINT ["/entrypoint.sh"]
