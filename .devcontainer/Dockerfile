FROM python:3.12-slim-bookworm

LABEL io.daqforge.devcontainer=true

RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/* \
    && useradd --create-home --user-group --shell /bin/bash vscode \
    && install -d -o vscode -g vscode /opt/daqforge-venv

ENV VIRTUAL_ENV=/opt/daqforge-venv \
    PIP_NO_CACHE_DIR=1
ENV PATH="${VIRTUAL_ENV}/bin:${PATH}"

USER vscode
