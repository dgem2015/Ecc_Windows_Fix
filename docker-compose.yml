# ─────────────────────────────────────────────
# Lightweight nginx file server
# Serves everything in ./files/ over HTTP :80
# ─────────────────────────────────────────────
FROM nginx:alpine

# Remove default nginx content
RUN rm -rf /usr/share/nginx/html/*

# Copy custom nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copy files to serve
COPY files/ /usr/share/nginx/html/files/

# Expose HTTP
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
