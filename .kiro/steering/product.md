---
inclusion: always
---

# Product Overview

**Conversor de Distância** is a modern web application for converting between different distance units. It provides an intuitive interface with real-time conversion capabilities and server-side validation.

## Core Features

- Distance unit conversions (meters, kilometers, miles, feet, centimeters, inches, yards)
- Real-time conversion preview via AJAX
- Conversion history stored in browser localStorage
- Server-side conversion API endpoint
- Responsive modern UI with gradient design and animations
- Unit swap functionality for quick reverse conversions

## Key Design Decisions

- **Server-side validation**: All conversions validated on the backend for accuracy
- **Dual conversion modes**: Traditional form submission and real-time AJAX conversion
- **Production-ready**: Designed for Kubernetes deployment on DigitalOcean
- **Modern UX**: Glass-morphism design, smooth animations, and keyboard shortcuts
- **Stateless**: No database required, conversion history stored client-side
