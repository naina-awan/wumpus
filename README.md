# Wumpus Logic Agent

A web-based implementation of the Wumpus World AI environment using propositional logic and resolution refutation to make intelligent decisions about hazard detection and navigation.

## Overview

This project implements an intelligent agent that navigates a grid world containing:

- **Pits** (P) - Deadly hazards that must be avoided
- **Wumpus** (W) - A dangerous monster
- **Safe cells** - Verified non-hazardous locations
- **Breeze & Stench percepts** - Sensory clues about nearby hazards

The agent uses a **knowledge base** with propositional logic and **resolution refutation** to infer which cells are safe and which contain hazards.

## Project Structure

```
.
├── backend/
│   ├── app.py                 # Flask backend with logic engine
│   ├── requirements.txt       # Python dependencies
│   └── static/
│       └── index.html         # Frontend UI
├── README.md                  # This file
```

## Technology Stack

**Backend:**

- Python 3.14+
- Flask 3.0.3
- Flask-CORS 4.0.1
- Gunicorn 22.0.0

**Frontend:**

- HTML5 / CSS3 / JavaScript (Vanilla)
- Bootstrap 5.3.3
- No build process required

## Local Development

### Prerequisites

- Python 3.8+
- pip

### Setup

1. **Clone the repository:**

   ```bash
   git clone https://github.com/naina-awan/wumpus.git
   cd wumpus/backend
   ```

2. **Create a virtual environment (optional but recommended):**

   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies:**

   ```bash
   pip install -r requirements.txt
   ```

4. **Run the Flask server:**

   ```bash
   python app.py
   ```

5. **Open in browser:**
   ```
   http://localhost:5000
   ```

## Usage

1. **Set Grid Size** — Enter desired rows and columns (3-10)
2. **Start Game** — Click the "Start Game" button to initialize
3. **Navigate** — Use arrow keys or buttons to move the agent
4. **Observe** — Watch as the agent discovers safe cells, hazards, and uses logic to reason about unknown cells
5. **Percepts** — View current sensory information (breeze, stench)
6. **Inference Steps** — Track the number of logical inference operations performed

### Grid Cell States

- **AGENT** (Purple) — Current agent position
- **HAZARD** (Red) — Confirmed pit or wumpus location
- **VISITED** (Cyan) — Previously explored cells
- **SAFE** (Green) — Logically verified safe cells

## Deployment

### Deploy Backend on Render

1. **Create a Render account** at [render.com](https://render.com)

2. **Connect your GitHub repository**

3. **Create a new Web Service** with these settings:
   - **Name**: `wumpus`
   - **Language**: Python 3
   - **Branch**: main
   - **Root Directory**: `backend`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `gunicorn app:app`
   - **Region**: Oregon (US West)

4. **Deploy** — Render will automatically build and start your service

5. **Copy the backend URL** — You'll need this for the frontend

### Deploy Frontend on Vercel

1. **Create a Vercel account** at [vercel.com](https://vercel.com)

2. **Add this configuration** to your `index.html` before the main script:

   ```html
   <script>
     window.API_BASE_URL = "https://your-render-backend.onrender.com";
   </script>
   ```

   Replace `your-render-backend` with your actual Render service name.

3. **Import your repository** into Vercel

4. **Deploy** — Vercel handles the rest

## API Endpoints

All endpoints are POST unless noted otherwise.

### `POST /api/new_game`

Start a new game session.

**Request:**

```json
{
  "rows": 4,
  "cols": 4,
  "session_id": "wumpus_abc123"
}
```

**Response:**

```json
{
  "rows": 4,
  "cols": 4,
  "agent": [0, 0],
  "visited": [[0, 0]],
  "safe_cells": [[0, 0]],
  "confirmed_hazards": [],
  "percepts": [],
  "total_inference_steps": 0,
  "message": "Game started!"
}
```

### `POST /api/move`

Move the agent in a direction.

**Request:**

```json
{
  "direction": "up",
  "session_id": "wumpus_abc123"
}
```

Directions: `"up"`, `"down"`, `"left"`, `"right"`

### `POST /api/shoot`

Fire an arrow in a direction.

**Request:**

```json
{
  "direction": "up",
  "session_id": "wumpus_abc123"
}
```

### `GET /api/state`

Retrieve the current game state.

**Query Parameters:**

- `session_id` — Session identifier

## How the Logic Engine Works

### Knowledge Base (KB)

- Stores propositional clauses in **Conjunctive Normal Form (CNF)**
- Contains rules about breeze/stench implications
- Tracks known facts about cell safety

### Resolution Refutation

To prove a query (e.g., "Is cell (2,3) safe?"):

1. Negate the query
2. Add negated query to KB
3. Apply resolution rule repeatedly until:
   - Empty clause found → Query is **true**
   - No new clauses produced → Query is **false**

### Propositional Variables

- `P_{row}_{col}` — Pit at location
- `W_{row}_{col}` — Wumpus at location
- `B_{row}_{col}` — Breeze at location
- `S_{row}_{col}` — Stench at location

## Performance Notes

- **In-memory sessions** — Game state stored in memory; sessions persist for app uptime only
- **Inference cap** — Max 2000 inference steps per query to prevent infinite loops
- **Session cleanup** — Sessions are cleaned up when the service restarts

For production with persistent state, consider adding:

- Redis for session storage
- PostgreSQL for game history
- Database schema for long-term state management

## Troubleshooting

### Local port already in use

Change port in `app.py`:

```python
app.run(host="0.0.0.0", port=8000)
```

### CORS errors in browser

CORS is already enabled; check that `API_BASE_URL` is correct in `index.html`.

### Render deployment fails

1. Verify Root Directory is set to `backend`
2. Check that `requirements.txt` exists and is valid
3. View deployment logs on Render dashboard

### Game state lost after refresh

This is normal — Flask stores sessions in memory. In production, use Redis or a database.

## License

School project (AI Assignment 6)

## Author

Naina Awan
