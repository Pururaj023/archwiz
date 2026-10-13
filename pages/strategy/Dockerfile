# Build context is the repository root:
#   docker build -f pages/strategy/Dockerfile -t gamepicks-strategy .
FROM nginxinc/nginx-unprivileged:1.27-alpine
COPY shared/style.css /usr/share/nginx/html/style.css
COPY pages/strategy/index.html /usr/share/nginx/html/index.html

EXPOSE 8080
