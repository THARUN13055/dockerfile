# Secure React Application with Nginx

## Table of Contents
- [Overview](#overview)
- [Docker Configuration](#docker-configuration)
- [Nginx Configuration](#nginx-configuration)
- [Security Measures](#security-measures)
- [Setup and Deployment](#setup-and-deployment)

## Overview
> Production-ready React application using multi-stage Docker build with Nginx serving and security best practices.

## Docker Configuration

### 🏗️ Build Stages

#### [Stage 1: Node Build]
```dockerfile
FROM node:14-alpine
# Purpose: React application build
```
- [x] npm ci for reproducible builds
- [x] babel plugin installation
- [x] cache cleaning

#### [Stage 2: GRPC Health Probe]
```dockerfile
FROM golang:1.21-alpine
# Purpose: Health monitoring binary
```
- [x] Version v0.4.19
- [x] Alpine-based for size optimization

#### [Stage 3: Production Nginx]
```dockerfile
FROM nginx:1.25-alpine
# Purpose: Production server
```
- [x] Non-root user execution
- [x] Custom security group
- [x] Minimal permissions

### 📁 Directory Structure
```
/var/cache/nginx/
│
├── client_temp/
├── proxy_temp/
├── fastcgi_temp/
├── uwsgi_temp/
└── scgi_temp/
```

### 🔧 Environment Configuration
```env
NODE_ENV=production
PORT=80
```

### 🏥 Health Check
```dockerfile
HEALTHCHECK --interval=30s --timeout=3s
CMD curl -f http://localhost:80/ || exit 1
```

## Nginx Configuration

### 🌐 Server Settings
```nginx
[Basic Configuration]
- Port: 80
- Server: localhost
- Root: /usr/share/nginx/html
```

### 📍 Location Blocks

#### [/ - Root Location]
```nginx
location / {
    try_files $uri /index.html;
}
```
- [x] SPA routing support
- [x] Security headers enabled

#### [/api/ - Backend Proxy]
```nginx
location /api/ {
    proxy_pass http://localhost:5000;
}
```
- [x] Header preservation
- [x] Proxy protection

#### [/health - Monitoring]
```nginx
location /health {
    return 200;
}
```
- [x] Minimal logging
- [x] Quick response

### 🔒 Security Headers
```nginx
[Implemented Headers]
✓ X-Content-Type-Options: nosniff
✓ X-XSS-Protection: 1; mode=block
✓ X-Frame-Options: DENY
✓ Content-Security-Policy
✓ Referrer-Policy
✓ HSTS
```

### ⚡ Performance
```nginx
[Gzip Configuration]
- Enabled for: CSS, JS, HTML, XML
- Min size: 10KB
```

## Security Measures

### 🐳 Docker Security
- [x] Non-root execution
- [x] Alpine base
- [x] Multi-stage build
- [x] Minimal permissions
- [x] File cleanup

### 🛡️ Nginx Security
- [x] Hidden server tokens
- [x] Protected hidden files
- [x] Header sanitization
- [x] Proxy protection
- [x] Security headers

### 📂 File System
- [x] Write restrictions
- [x] Log protection
- [x] Temp directory security
- [x] PID file protection

## Setup and Deployment

### 📋 Prerequisites
- [ ] Docker
- [ ] Docker Compose (optional)
- [ ] Node.js 14+ (development)

### 🚀 Build Commands
```bash
# Build
docker build -t secure-react-app .

# Run
docker run -p 80:80 secure-react-app
```

### ✅ Verification
1. [ ] Container logs check
2. [ ] Health endpoint test
3. [ ] Security header verification
4. [ ] API proxy test

## Troubleshooting

### 🔍 Common Issues

#### [Permission Errors]
```bash
# Check ownership
ls -la /var/cache/nginx

# Verify SELinux
sestatus
```

#### [Startup Failures]
```bash
# Check logs
docker logs <container-id>

# Verify ports
netstat -tulpn
```

#### [Proxy Issues]
```bash
# Test backend
curl http://localhost:5000

# Check nginx config
nginx -t
```

## Additional Notes

### 📝 Key Points
- [x] Production optimized
- [x] Security-first approach
- [x] Proper directory permissions
- [x] Minimal logging

### 🔄 Updates
> Check security policy for latest updates

---

*For security reports or updates, please open an issue or contact the security team.*

[Documentation Status: Up to Date]
[Last Updated: 2024-10-22]