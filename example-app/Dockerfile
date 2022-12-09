FROM python:3.9.15-alpine

RUN apk update && \
apk add gcc python3-dev libc-dev libpq-dev libffi-dev poetry

ENV PATH=$PATH:/root/.local/bin

COPY . app/

WORKDIR /app

RUN poetry install --no-root

ENTRYPOINT [ "poetry", "run", "task", "prod" ]