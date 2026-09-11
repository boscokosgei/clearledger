
FROM nginx:1.27-alpine

# Upgrade all Alpine packages to latest security patches.
# docker build --no-cache in CI ensures this always runs fresh (no stale layer).
RUN apk update && apk upgrade --no-cache

# Security: run as non-root with explicit numeric UID/GID
RUN addgroup -g 10001 -S appuser && adduser -u 10001 -S appuser -G appuser

COPY nginx.conf /etc/nginx/nginx.conf
COPY src/ /usr/share/nginx/html/

# nginx needs to write to these paths even as non-root
RUN chown -R appuser:appuser /usr/share/nginx/html \
    /var/cache/nginx \
    /var/log/nginx \
    /etc/nginx/conf.d && \
    touch /var/run/nginx.pid && \
    chown appuser:appuser /var/run/nginx.pid

USER 10001

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
  CMD ["wget", "-q", "--spider", "http://localhost:8080/"]

CMD ["nginx", "-g", "daemon off;"]
