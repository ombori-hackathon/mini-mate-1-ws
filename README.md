# MiniMate - Your Desktop Companion

An adorable AI-powered desktop buddy that observes your workflow and provides helpful, contextual hints when you need them most.

## Overview

MiniMate is a cute animated companion that:
- Watches what you're working on (app, window, typing patterns)
- Detects when you're struggling (error keywords, back-and-forth switching)
- Offers timely, relevant tips based on your actual behavior
- Stays quiet when you're in flow

## Project Structure

```
hackathon-ws/
├── apps/macos-client/    # SwiftUI desktop app (submodule)
├── services/api/         # FastAPI Python backend (submodule)
├── specs/                # Feature specifications
└── docker-compose.yml    # PostgreSQL database
```

## Quick Start

### Prerequisites
- Docker & Docker Compose
- Python 3.12+ with [uv](https://github.com/astral-sh/uv)
- Xcode / Swift toolchain (macOS 14+)
- Ollama (for AI-powered hints)

### Setup

```bash
# Clone with submodules
git clone --recursive <repo-url>
cd hackathon-ws

# Start database
docker compose up -d

# Start API server (terminal 1)
cd services/api
uv sync
uv run fastapi dev

# Run Swift client (terminal 2)
cd apps/macos-client
swift run MiniMate1Client
```

## Architecture

### Swift Client
Native macOS app with:
- Floating window companion
- Activity tracking & struggle detection
- Scheduled event reminders
- Local preferences storage

### Python API
FastAPI backend with:
- Activity logging & analysis
- AI-powered hint generation (via Ollama)
- User preferences management
- PostgreSQL persistence

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/activities/report` | Report user activity batch |
| GET | `/hints/{device_id}/pending` | Get pending hints |
| PATCH | `/hints/{hint_id}/status` | Update hint status |
| GET | `/preferences/{device_id}` | Get user preferences |
| PATCH | `/preferences/{device_id}` | Update preferences |

API docs available at http://localhost:8000/docs

## Development

See individual repo READMEs:
- [Swift Client](apps/macos-client/README.md)
- [API Service](services/api/README.md)

## License

MIT
