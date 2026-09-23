FROM node:20.12.2-bookworm-slim AS build

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM caddy:2.11.4-alpine

RUN addgroup -S app \
    && adduser -S -G app app \
    && mkdir -p /srv /config /data /var/log/caddy \
    && chown -R app:app /srv /config /data /var/log/caddy

COPY --from=build --chown=app:app /app/dist/microcrm/browser/ /srv/
COPY Caddyfile /etc/caddy/Caddyfile

ENV XDG_CONFIG_HOME=/config \
    XDG_DATA_HOME=/data

USER app

EXPOSE 8080

ENTRYPOINT ["caddy", "run", "--config", "/etc/caddy/Caddyfile", "--adapter", "caddyfile"]
