# Build stage
FROM node:14-alpine AS build
LABEL org.paynpro.image.authors="Tharun G <megatharun13055@gmail.com, tharun@axonaio.com>"
LABEL org.paynpro.image.created="2024-10-22"

# Set the working directory
WORKDIR /react-app

# Copy package files for better caching
COPY package*.json ./

# Install dependencies with specific version and security audit
RUN npm install --force \
    && npm ci --production \
    && npm install --save-dev @babel/plugin-transform-private-property-in-object@latest \
    && npm cache clean --force

# Copy the rest of the application code
COPY . .

# Build the React application
RUN npm run build
    
FROM golang:1.21-alpine AS grpc-health-probe-build

# Set working directory
WORKDIR /go/src/grpc-health-probe

# Install necessary build tools
RUN apk add --no-cache git build-base

# Build grpc-health-probe with explicit output path
RUN go install -v github.com/grpc-ecosystem/grpc-health-probe@v0.4.19

# Production stage
FROM nginx:1.25-alpine

# Add security headers and create non-root user
RUN apk add --no-cache curl \
    && addgroup -S appgroup && adduser -S appuser -G appgroup

# Create required directories and set permissions
RUN mkdir -p /var/cache/nginx \
    && mkdir -p /var/cache/nginx/client_temp \
    && mkdir -p /var/cache/nginx/proxy_temp \
    && mkdir -p /var/cache/nginx/fastcgi_temp \
    && mkdir -p /var/cache/nginx/uwsgi_temp \
    && mkdir -p /var/cache/nginx/scgi_temp \
    && mkdir -p /var/log/nginx \
    && mkdir -p /bin \
    && chmod 755 /bin \
    && chown -R appuser:appgroup /var/cache/nginx \
    && chown -R appuser:appgroup /var/log/nginx \
    && chmod -R 755 /var/cache/nginx \
    && touch /var/run/nginx.pid \
    && chown -R appuser:appgroup /var/run/nginx.pid \
    && rm -rf /usr/share/nginx/html/* \
    && rm /etc/nginx/conf.d/default.conf

# Copy built assets and configuration
COPY --from=build --chown=appuser:appgroup /react-app/build /usr/share/nginx/html
COPY --from=grpc-health-probe-build /go/bin/grpc-health-probe /bin/grpc_health_probe
COPY --chown=appuser:appgroup nginx.conf /etc/nginx/conf.d/nginx.conf

# Verify health probe exists and set permissions
RUN if [ ! -f /bin/grpc_health_probe ]; then echo "Health probe not found!" && exit 1; fi && \
    chmod 755 /bin/grpc_health_probe && \
    chmod -R 755 /usr/share/nginx/html && \
    chown -R appuser:appgroup /usr/share/nginx/html

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
    CMD curl -f http://localhost:80/ || exit 1

# Switch to non-root user
USER appuser

# Expose port
EXPOSE 80

# Define environment variables
ENV NODE_ENV=production \
    PORT=80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]