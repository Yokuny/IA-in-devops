---
inclusion: always
---

# Project Structure

## Root Organization

Single-page Flask application for distance conversion:
- `main.py`: Flask application entry point
- `requirements.txt`: Python dependencies
- `Dockerfile`: Container configuration
- `manifest.yaml`: Kubernetes deployment configuration

## Application Structure

### Core Application Files

- `main.py`: Flask app with routes, conversion logic, and server info display
- `requirements.txt`: Flask, Gunicorn, and dependencies
- `Dockerfile`: Multi-stage build for production deployment
- `manifest.yaml`: Kubernetes Deployment, Service, and Namespace configuration

### Directory Layout

```
.
├── main.py              # Flask application with routes and conversion logic
├── requirements.txt     # Python dependencies
├── Dockerfile          # Container image definition
├── manifest.yaml       # Kubernetes deployment config
├── templates/          # Jinja2 HTML templates
│   └── index.html     # Single-page application template
└── static/            # Static assets
    ├── css/
    │   └── style.css  # Modern gradient design with animations
    └── js/
        └── converter.js  # AJAX conversion and client-side logic
```

## Architectural Patterns

### Simple Flask Application
1. **Routes** → Handle HTTP requests (GET for page, POST for form, POST for API)
2. **Conversion Logic** → Dictionary-based conversion factors in main.py
3. **Templates** → Single Jinja2 template with Bootstrap 5 and custom CSS
4. **Client-side** → JavaScript for AJAX, real-time conversion, and history

### Key Conventions

- **Conversion dictionary**: All conversion factors stored in a single dictionary
- **Dual endpoints**: Traditional form POST (`/`) and AJAX API (`/convert-api`)
- **Server info**: Hostname and IP displayed in footer for Kubernetes visibility
- **Error handling**: Try-catch blocks for invalid input with user-friendly messages
- **Client-side storage**: Conversion history stored in localStorage (no database)

### Configuration Management

- Environment variables: `PYTHONUNBUFFERED`, `HOST`, `PORT`
- Gunicorn configuration: 4 workers, bind to 0.0.0.0:8000
- Kubernetes: 2 replicas, LoadBalancer service, health probes

### Deployment Patterns

- **Docker**: Python 3.11-slim base image with Gunicorn
- **Kubernetes**: Namespace isolation, resource limits, liveness/readiness probes
- **DigitalOcean**: Container registry integration with imagePullSecrets
- **Scaling**: 2 replicas with resource requests (128Mi/100m) and limits (256Mi/200m)
