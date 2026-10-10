# Build context is the repository root:
#   docker build -f pages/action/Dockerfile -t gamepicks-action .
FROM nginxinc/nginx-unprivileged:1.27-alpine
COPY shared/style.css /usr/share/nginx/html/style.css
COPY pages/action/index.html /usr/share/nginx/html/index.html
EXPOSE 8080
