---
inclusion: always
---

# Technology Stack

## Core Framework & Libraries

- **Flask 3.1.2**: Web framework
- **Jinja2 3.1.6**: Template engine for web UI
- **Gunicorn 23.0.0**: WSGI server for production
- **Werkzeug 3.1.3**: WSGI utility library

## Frontend

- **Bootstrap 5.1.0**: CSS framework via CDN
- **Font Awesome 6.0.0**: Icons via CDN
- **Google Fonts (Inter)**: Typography
- **Vanilla JavaScript**: Client-side logic (no frameworks)

## Infrastructure

- **Docker**: Containerization with Python 3.11-slim base
- **Kubernetes**: Orchestration on DigitalOcean
- **DigitalOcean Container Registry**: Image storage
- **LoadBalancer Service**: External access

## Testing

- **pytest**: Testing framework (configured but no tests yet)

## Common Commands

### Development
```bash
# Run locally with Flask dev server
python main.py

# Run with Gunicorn locally
gunicorn -w 4 -b 0.0.0.0:8000 main:app
```

### Testing
```bash
# Run all tests
pytest

# Run with verbose output
pytest -v
```

### Docker
```bash
# Build image
docker build -t conversao-distancia .

# Run container
docker run -p 8000:8000 conversao-distancia

# Build and push to DigitalOcean registry
docker build -t registry.digitalocean.com/conversao-distancia-registry/conversao-distancia:latest .
docker push registry.digitalocean.com/conversao-distancia-registry/conversao-distancia:latest
```

### Kubernetes
```bash
# Apply manifest
kubectl apply -f manifest.yaml

# Check deployment
kubectl get pods -n conversao-distancia
kubectl get svc -n conversao-distancia

# View logs
kubectl logs -n conversao-distancia -l app=conversao-distancia

# Delete deployment
kubectl delete -f manifest.yaml
```

## Python Version

- **Python 3.11+** required (using 3.11-slim in Docker)
