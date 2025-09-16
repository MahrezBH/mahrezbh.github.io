---
title: "How to Tunnel Your Full-Stack Project to the Internet: A Complete Guide"
date: 2025-01-25
categories: [development, devops, networking]
tags: [tunneling, ngrok, docker, fullstack, development, caddy, reverse-proxy]
---

*Ever wanted to share your local development project with the world? Whether it's for demos, client reviews, or testing with remote users, tunneling your full-stack application to the internet is easier than you think. Let's dive into the complete process, from setup to troubleshooting mixed content issues.*

## Why Tunnel Your Local Project?

Before we dive in, let's understand why you'd want to expose your local development environment:

- **Client Demos**: Show your work-in-progress to stakeholders instantly
- **Remote Testing**: Let team members test features from anywhere
- **API Testing**: Share your backend with frontend developers
- **Mobile Testing**: Test your web app on real devices
- **Integration Testing**: Connect with external services that need webhooks

## The Architecture: How It All Works

Here's what we're building:

```
Internet → ngrok (HTTPS) → Caddy (Reverse Proxy) → Your Services
                                    ├── /api/* → Django Backend (Port 8000)
                                    ├── /media/* → Django Backend (Port 8000)  
                                    └── /* → React Frontend (Port 5173)
```

**Key Components:**
- **ngrok**: Creates secure HTTPS tunnel from internet to your local machine
- **Caddy**: Reverse proxy that routes requests to appropriate services
- **Django Backend**: Your API server
- **React Frontend**: Your user interface

## Prerequisites

- Docker and Docker Compose installed
- ngrok account (free tier works fine)
- A full-stack project (we'll use Django + React as example)

## Step 1: Setting Up ngrok

### Install and Configure ngrok

```bash
# Install ngrok (Ubuntu/Debian)
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null
echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list
sudo apt update && sudo apt install ngrok

# Configure with your auth token
ngrok config add-authtoken YOUR_AUTH_TOKEN
```

### Test ngrok

```bash
# Test with a simple web server
python -m http.server 8080
# In another terminal
ngrok http 8080
```

## Step 2: Setting Up Caddy Reverse Proxy

Create a `Caddyfile` in your project root:

```caddyfile
:8080 {
    # API routes go to Django backend
    reverse_proxy /api/* localhost:8000 {
        header_up X-Forwarded-Proto {scheme}
        header_up X-Forwarded-For {remote}
        header_up X-Forwarded-Host {host}
    }
    
    # Media files go to Django backend
    reverse_proxy /media/* localhost:8000 {
        header_up X-Forwarded-Proto {scheme}
        header_up X-Forwarded-For {remote}
        header_up X-Forwarded-Host {host}
    }
    
    # Everything else goes to React frontend
    reverse_proxy /* localhost:5173
}
```

Install and run Caddy:

```bash
# Install Caddy
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy

# Run Caddy
caddy run --config Caddyfile --adapter caddyfile
```

## Step 3: Configuring Your Backend (Django)

### Update Django Settings

```python
# settings.py
import os

# Allow all hosts for tunneling
ALLOWED_HOSTS = ['*']

# CORS settings for frontend
CORS_ALLOW_ALL_ORIGINS = True

# Media files configuration
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

# HTTPS detection for tunnel
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
SECURE_SSL_REDIRECT = False  # Let Caddy handle SSL
```

### Update URL Configuration

```python
# urls.py
from django.contrib import admin
from django.urls import path, include
from django.conf.urls.static import static
from django.conf import settings

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

## Step 4: Configuring Your Frontend (React)

### Smart URL Detection

The key to making your frontend work with tunneling is smart URL detection:

```typescript
// api/axios.ts
import axios from 'axios';

// Auto-detect API URL based on protocol
const getBaseURL = () => {
    // If we're in production or accessed via tunnel (https), use relative URL
    if (window.location.protocol === 'https:' || import.meta.env.PROD) {
        return '/api';  // Relative URL goes through Caddy proxy
    }
    // For local development, use environment variable or default local IP
    return (import.meta.env.VITE_API_URL || 'http://localhost:8000') + '/api';
};

const api = axios.create({
    baseURL: getBaseURL(),
    timeout: 10000,
});

export default api;
```

### Image URL Handling

```typescript
// components/ImageComponent.tsx
const getImageUrl = (imagePath: string) => {
    if (imagePath.startsWith('http')) return imagePath;
    
    // Auto-detect base URL based on protocol
    const baseUrl = (window.location.protocol === 'https:' || import.meta.env.PROD) 
        ? ''  // Use relative URL for tunnel/production
        : 'http://localhost:8000';  // Fallback for local development
    
    return `${baseUrl}/media/${imagePath}`;
};
```

## Step 5: Docker Compose Configuration

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - db_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  backend:
    build: ./backend
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - ./backend/media:/app/media
    environment:
      - DEBUG=${DEBUG}
      - DB_NAME=${DB_NAME}
      - DB_USER=${DB_USER}
      - DB_PASSWORD=${DB_PASSWORD}
      - DB_HOST=${DB_HOST}
      - DB_PORT=${DB_PORT}
      - DJANGO_SECRET_KEY=${DJANGO_SECRET_KEY}
      - DJANGO_ALLOWED_HOSTS=*
    ports:
      - "8000:8000"
    depends_on:
      - db

  frontend:
    build: ./frontend
    ports:
      - "5173:5173"
    depends_on:
      - backend

  caddy:
    image: caddy:2.7-alpine
    ports:
      - "8080:8080"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
    depends_on:
      - backend
      - frontend
    restart: unless-stopped

volumes:
  db_data:
```

## Step 6: Running Your Tunnel

### Quick Start Script

Create a simple script to start everything:

```bash
#!/bin/bash
# tunnel.sh

echo "🚀 Starting tunnel setup..."

# Start services if not running
if ! docker compose ps | grep -q "Up"; then
    echo "📦 Starting Docker services..."
    docker compose up -d
    sleep 8
fi

# Start Caddy if not running
if ! netstat -tlnp 2>/dev/null | grep -q ":8080"; then
    echo "🔧 Starting Caddy reverse proxy..."
    caddy run --config Caddyfile --adapter caddyfile &
    sleep 3
fi

# Start ngrok tunnel
echo "🌐 Starting ngrok tunnel..."
ngrok http 8080 --log=stdout &

# Wait for tunnel to establish
sleep 5

# Get tunnel URL
TUNNEL_URL=$(curl -s http://localhost:4040/api/tunnels 2>/dev/null | jq -r '.tunnels[0].public_url' 2>/dev/null)

if [ "$TUNNEL_URL" != "null" ] && [ -n "$TUNNEL_URL" ]; then
    echo ""
    echo "✅ Tunnel Active!"
    echo "🌍 Public URL: $TUNNEL_URL"
    echo "🔧 ngrok Web UI: http://localhost:4040"
    echo "🏠 Local Access: http://localhost:8080"
else
    echo "❌ Failed to create tunnel"
    exit 1
fi
```

### Usage

```bash
# Make script executable
chmod +x tunnel.sh

# Start tunnel
./tunnel.sh

# Stop everything
docker compose down
pkill ngrok
pkill caddy
```

## Common Issues and Solutions

### Mixed Content Errors

**Problem**: Browser blocks HTTP requests from HTTPS pages.

**Solution**: Use relative URLs and smart protocol detection:

```typescript
// ❌ Bad - hardcoded HTTP
const apiUrl = 'http://192.168.1.100:8000/api';

// ✅ Good - smart detection
const apiUrl = window.location.protocol === 'https:' ? '/api' : 'http://localhost:8000/api';
```

### Media Files Not Loading

**Problem**: Images return HTML instead of actual files.

**Solution**: Ensure Caddy routes media files to Django:

```caddyfile
# Caddyfile
:8080 {
    reverse_proxy /media/* localhost:8000 {  # ← This is crucial
        header_up X-Forwarded-Proto {scheme}
    }
    reverse_proxy /api/* localhost:8000
    reverse_proxy /* localhost:5173
}
```

### CORS Issues

**Problem**: Frontend can't access backend API.

**Solution**: Configure Django CORS properly:

```python
# settings.py
CORS_ALLOW_ALL_ORIGINS = True
CORS_ALLOW_CREDENTIALS = True
```

## Security Considerations

### Development Only

This setup is for **development and demos only**. For production:

- Use proper domain names
- Set up SSL certificates
- Configure proper CORS policies
- Use environment-specific settings

### ngrok Security

- Free tier URLs change on restart
- Consider ngrok Pro for custom domains
- Be mindful of what you expose

## Troubleshooting Checklist

- [ ] ngrok is running and shows online status
- [ ] Caddy is listening on port 8080
- [ ] Docker services are running (`docker compose ps`)
- [ ] Frontend uses relative URLs for HTTPS
- [ ] Backend allows all hosts (`ALLOWED_HOSTS = ['*']`)
- [ ] CORS is configured properly
- [ ] Media files route to Django, not frontend

## Quick Commands Reference

```bash
# Start everything
./tunnel.sh

# Stop everything
docker compose down && pkill ngrok && pkill caddy

# Check status
docker compose ps
netstat -tlnp | grep :8080
curl http://localhost:4040/api/tunnels

# View logs
docker compose logs backend
docker compose logs frontend
```

## Final Thoughts

Tunneling your full-stack project to the internet opens up incredible possibilities for collaboration, demos, and testing. The key is proper configuration of your reverse proxy and smart URL detection in your frontend.

Remember:
- **Security first**: This is for development only
- **Smart URLs**: Detect protocol, don't hardcode
- **Proper routing**: Media files go to backend, not frontend
- **Monitor everything**: Use the ngrok web interface

Now go forth and share your amazing projects with the world! 🚀

*What's your experience with tunneling? Any tips or tricks to share? Let me know in the comments!*