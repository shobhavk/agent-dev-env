# ============================================================
# Agent Development Environment
# Python 3.13 (latest stable) + PostgreSQL client + dev tools
# ============================================================
FROM python:3.13-slim-bookworm

# Build args
ARG USERNAME=agentdev
ARG USER_UID=1000
ARG USER_GID=1000

# ── System packages ─────────────────────────────────────────
RUN apt-get update && apt-get install -y --no-install-recommends \
    # PostgreSQL client + dev headers
    postgresql-client \
    libpq-dev \
    # Build essentials
    build-essential \
    gcc \
    g++ \
    # Git & shell tools
    git \
    curl \
    wget \
    vim \
    nano \
    htop \
    tree \
    jq \
    # Networking / debugging
    iputils-ping \
    net-tools \
    telnet \
    # SSH (for remote dev)
    openssh-client \
    # Clean up
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# ── Non-root user (matches VS Code dev containers convention) ─
RUN groupadd --gid ${USER_GID} ${USERNAME} \
    && useradd --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} \
    && apt-get update && apt-get install -y sudo \
    && echo "${USERNAME} ALL=(root) NOPASSWD:ALL" > /etc/sudoers.d/${USERNAME} \
    && chmod 0440 /etc/sudoers.d/${USERNAME} \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# ── Python tooling ───────────────────────────────────────────
RUN pip install --upgrade pip setuptools wheel

# Core agent / LLM packages
COPY requirements.txt /tmp/requirements.txt
RUN pip install --no-cache-dir -r /tmp/requirements.txt

# ── Working directory ────────────────────────────────────────
WORKDIR /workspace
RUN chown ${USERNAME}:${USERNAME} /workspace

USER ${USERNAME}

# Shell customisation
RUN echo 'export PS1="\[\033[1;36m\](agent-dev)\[\033[0m\] \w $ "' >> ~/.bashrc \
    && echo 'alias ll="ls -lah --color=auto"' >> ~/.bashrc \
    && echo 'alias gs="git status"' >> ~/.bashrc

EXPOSE 8000 7860 8080

CMD ["bash"]
