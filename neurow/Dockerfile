ARG BUILDER_IMAGE=elixir:1.17-slim

FROM ${BUILDER_IMAGE} AS builder

RUN apt-get update \
  && apt-get install -y --no-install-recommends git build-essential ca-certificates \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

RUN mkdir /app
WORKDIR /app
ENV MIX_ENV=prod

RUN mix local.hex --force

COPY mix.exs mix.lock /app/
RUN mix deps.get

COPY config /app/config/
COPY lib /app/lib/

ARG GIT_COMMIT_SHA1=no_commit
RUN mix release

FROM ${BUILDER_IMAGE}

RUN apt-get update \
  && apt-get install -y --no-install-recommends curl dnsutils

RUN mkdir /app
WORKDIR /app

COPY start.sh /start.sh
COPY --from=builder /app/_build/prod/rel/neurow /app/

ENV RELEASE_TMP=/tmp/
ENV RELEASE_COOKIE=changme

CMD ["/start.sh" ]