# Build context is the repository root:
#   docker build -f pages/indie/Dockerfile -t gamepicks-indie .
FROM nginxinc/nginx-unprivileged:1.27-alpine
COPY shared/style.css /usr/share/nginx/html/style.css
COPY pages/indie/index.html /usr/share/nginx/html/index.html
EXPOSE 8080
