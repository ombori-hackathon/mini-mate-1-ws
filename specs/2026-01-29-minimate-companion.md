# MiniMate Desktop Companion

## Summary
An adorable AI-powered desktop buddy that observes your workflow and provides helpful, contextual hints when you need them most.

## Problem
- Users get stuck on problems without realizing helpful resources exist
- Generic productivity tips are annoying and irrelevant
- Break reminders don't consider what you're actually doing

## Solution
A cute animated companion that:
- Watches what you're working on (app, window, typing patterns)
- Detects when you're struggling (error keywords, back-and-forth switching)
- Offers timely, relevant tips based on your actual behavior
- Stays quiet when you're in flow

## Architecture

### Swift Client (`apps/macos-client/`)
```
Sources/
├── Activity/           # App tracking, struggle detection
├── Animation/          # Character states and sprites
├── Companion/          # Floating window and UI
├── Events/             # Scheduled reminders
├── Models/             # Data structures
├── Networking/         # API communication
├── Preferences/        # User settings
└── Utilities/          # Screen, window, system observers
```

### Python API (`services/api/`)
```
app/
├── models/             # ActivityLog, Hint, UserPreferences
├── routers/            # activity, hints, preferences, events
├── schemas/            # Request/response validation
└── services/           # AIService, HintGenerator
```

## Behavior Detection

| Behavior | Trigger | Priority |
|----------|---------|----------|
| Debugging | Error keywords, struggle_score >= 4 | High |
| Coding | In code editor (Cursor, VS Code, etc.) | Medium |
| Researching | Browser with search/docs keywords | Medium |
| Distracted | app_switches > 10, back_and_forth >= 2 | High |
| Browsing | General browser usage | Medium |
| Communication | Slack, Discord, Mail, etc. | Medium |

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/activities/report` | Report user activity batch |
| GET | `/hints/{device_id}/pending` | Get pending hints |
| PATCH | `/hints/{hint_id}/status` | Update hint status |
| GET | `/preferences/{device_id}` | Get user preferences |
| PATCH | `/preferences/{device_id}` | Update preferences |
| POST | `/events/reminder` | Send event reminder |

## Configuration

| Setting | Default | Description |
|---------|---------|-------------|
| `OLLAMA_MODEL` | llama3.2 | AI model for hint generation |
| `work_session_minutes` | 30 | Session length before break hint |
| `max_hints_per_hour` | 3 | Rate limit for hints |

## Testing

```bash
# API
cd services/api && uv run pytest

# Swift
cd apps/macos-client && swift test

# Manual test
curl -X POST http://localhost:8000/activities/report \
  -H "Content-Type: application/json" \
  -d '{"device_id": "test", "activities": [{"app_name": "Chrome", "window_title": "error fix - Google", "started_at": "2026-01-29T12:00:00", "struggle_score": 5}]}'
```

## Learnings

1. **Electron apps** (Cursor, VS Code) don't expose window titles via Accessibility API - use CGWindowListCopyWindowInfo instead
2. **AI prompts** must explicitly say "RESPOND WITH JSON ONLY" or Ollama adds explanations
3. **Behavior detection order matters** - check coding before researching to avoid false positives from " - " in titles
4. **Rate limiting** needs minimum 5 seconds between hints for good UX
