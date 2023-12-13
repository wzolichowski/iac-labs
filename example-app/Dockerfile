FROM python:3.9.18-alpine

RUN apk update && \
    apk add gcc python3-dev libc-dev libpq-dev libffi-dev poetry

ENV PATH=$PATH:/root/.local/bin \
    POETRY_NO_INTERACTION=1 \
    POETRY_VIRTUALENVS_IN_PROJECT=1 \
    POETRY_VIRTUALENVS_CREATE=1 \
    POETRY_CACHE_DIR=/tmp/poetry_cache

COPY . app/

WORKDIR /app

RUN poetry env use 3.9 && \
    poetry install --no-root

ENTRYPOINT [ "poetry", "run", "task", "prod" ]