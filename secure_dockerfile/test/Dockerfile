# Build stage
FROM node:20-alpine AS build
LABEL org.paynpro.image.authors="Tharun G"

WORKDIR /react-app

COPY package*.json ./
RUN npm install
COPY . .
ENV NODE_OPTIONS=--max-old-space-size=4096
RUN npm run build

# Production stage
FROM nginx:1.25-alpine

# Create non-root user and required directories
RUN adduser -D tharun && \
    mkdir -p /tmp/nginx && \
    mkdir -p /usr/share/nginx/html && \
    mkdir -p /var/cache/nginx && \
    mkdir -p /var/run/nginx && \
    mkdir -p /etc/nginx/conf.d

# Remove default configs
RUN rm -rf /usr/share/nginx/html/* && \
    rm /etc/nginx/conf.d/default.conf

# Copy build artifacts
COPY --from=build --chown=tharun:tharun /react-app/dist /usr/share/nginx/html

# Set permissions for dist files
RUN find /usr/share/nginx/html -type f -exec chmod 444 {} \; && \
    find /usr/share/nginx/html -type d -exec chmod 555 {} \;

# Copy and configure nginx
COPY --chown=root:root nginx.conf /etc/nginx/nginx.conf
RUN chmod 644 /etc/nginx/nginx.conf && \
    chown -R tharun:tharun /tmp/nginx && \
    chmod -R 777 /tmp/nginx && \
    chown -R tharun:tharun /var/cache/nginx && \
    chmod -R 755 /var/cache/nginx && \
    chown -R tharun:tharun /var/run/nginx && \
    chmod -R 755 /var/run/nginx && \
    chown -R tharun:tharun /usr/share/nginx/html && \
    chmod -R 755 /usr/share/nginx/html

USER tharun

CMD ["nginx", "-g", "daemon off;"]

# # Build stage
# FROM node:20-alpine AS build
# LABEL org.paynpro.image.authors="Tharun G"

# WORKDIR /react-app

# COPY package*.json ./
# RUN npm install
# COPY . .
# ENV NODE_OPTIONS=--max-old-space-size=4096
# RUN npm run build

# # Production stage
# FROM nginx:1.25-alpine

# # Create non-root user and required directories
# RUN adduser -D tharun && \
#     mkdir -p /tmp/nginx && \
#     chown -R tharun:tharun /tmp/nginx && \
#     chmod -R 777 /tmp/nginx

# # Remove default configs
# RUN rm -rf /usr/share/nginx/html/* && \
#     rm /etc/nginx/conf.d/default.conf

# # Copy build artifacts and set permissions
# COPY --from=build --chown=tharun:tharun /react-app/dist /usr/share/nginx/html
# RUN find /usr/share/nginx/html -type f -exec chmod 444 {} \; && \
#     find /usr/share/nginx/html -type d -exec chmod 555 {} \;

# # Copy nginx config and set permissions
# COPY --chown=root:root nginx.conf /etc/nginx/nginx.conf
# RUN chmod 644 /etc/nginx/nginx.conf && \
#     chown -R tharun:tharun /tmp/nginx && \
#     chmod -R 777 /tmp/nginx && \
#     chown -R tharun:tharun /var/cache/nginx && \
#     chmod -R 755 /var/cache/nginx && \
#     chown -R tharun:tharun /var/run && \
#     chmod -R 755 /var/run && \
#     chown -R tharun:tharun /usr/share/nginx/html && \
#     chmod -R 755 /usr/share/nginx/html

# USER tharun
# EXPOSE 5713

# CMD ["nginx", "-g", "daemon off;"]