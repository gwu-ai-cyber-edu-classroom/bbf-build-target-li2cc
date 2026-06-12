# START_APP.md — how to run and probe this app

> **Build team:** fill in every `<...>` below once your app runs. Other teams use this file to
> start your app and probe it during Break. Keep it accurate — a break is filed against the app a
> breaker can actually start from these instructions.

## What this app is

- **App:** a notes / journal app with login (menu #2)
- **Stack:** Python + Flask

## Start it

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Run it (creates/reseeds notes.db on startup, then serves on port 8000)
python app.py
```

- **Base URL:** http://localhost:8000
- **Stop it:** Ctrl-C in the terminal running it.

## How to interact with it

- **Main endpoints / pages:**
  - `GET /` — login form, or your own notes list once logged in — open http://localhost:8000/
  - `POST /login` — log in with `username` + `password` form fields
  - `GET /logout` — log out
  - `GET /notes/<id>` — view a single note by numeric id — e.g. `GET /notes/1`
  - `POST /notes` — create a note with `title` + `body` form fields (must be logged in)
- **Accounts / credentials for legitimate use:**
  - `alice` / `alice123`
  - `bob` / `bob123`
- **A benign request that should succeed:**

  ```bash
  # Log in as alice (saving the session cookie), then list her notes:
  curl -s -c cookies.txt -X POST -d "username=alice&password=alice123" http://localhost:8000/login
  curl -s -b cookies.txt http://localhost:8000/
  ```

## For breakers

Attack this **running app over HTTP** — do **not** read this repo's source or `secret/` to find a
break. See [AGENTS_BREAK.md](AGENTS_BREAK.md) for the rules and your AI agent's instructions, and
[SPEC.md](SPEC.md) for the five properties (P1–P5) you are probing for.
