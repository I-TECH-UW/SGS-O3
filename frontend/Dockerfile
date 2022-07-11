# syntax=docker/dockerfile:1.3
FROM node:lts-alpine as dev
ARG APP_SHELL_VERSION=next
RUN mkdir -p /app
WORKDIR /app
COPY spa-build-config.json .
RUN npx openmrs@$APP_SHELL_VERSION build --build-config spa-build-config.json --target ./spa
RUN npx openmrs@$APP_SHELL_VERSION assemble --mode config --config spa-build-config.json --target ./spa

FROM nginx:1.21-alpine

RUN apk update && \
    apk upgrade && \
    # add more utils for sponge to support our startup script
    apk add --no-cache moreutils

# clear any default files installed by nginx
RUN rm -rf /usr/share/nginx/html/*

COPY startup.sh /usr/local/bin/startup.sh
RUN chmod +x /usr/local/bin/startup.sh

COPY nginx.conf /etc/nginx/nginx.conf

COPY --from=dev /app/spa /usr/share/nginx/html

CMD ["/usr/local/bin/startup.sh"]