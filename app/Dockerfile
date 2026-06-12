# ==========================================
# 1단계: 빌드 스테이지
# ==========================================
FROM python:3.13-slim AS builder
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

WORKDIR /workspace

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

COPY pyproject.toml uv.lock ./
RUN uv venv /workspace/.venv && \
    uv pip install --no-cache -r pyproject.toml

# ==========================================
# 2단계: 런타임 스테이지
# ==========================================
FROM python:3.13-slim
WORKDIR /workspace

# [핵심 교정] 소스코드를 먼저 복사하여 호스트의 .venv가 들어오게 만듦
COPY . .

# [핵심 교정] 그 후, 빌드 스테이지에서 완성된 '순수 Linux 가상환경'을 덮어씌워 호스트 오염을 완전 차단
COPY --from=builder /workspace/.venv /workspace/.venv

ENV PATH="/workspace/.venv/bin:$PATH"
ENV PYTHONPATH=/workspace