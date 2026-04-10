# InciScan — Real-Time Incident Analysis System

> Detect threats before they escalate. React in real time.

InciScan is a full-stack ML-powered platform for real-time incident detection and analysis. It processes live video feeds using YOLOv8 to identify crowd density anomalies, suspicious behavior, and violence — then streams alerts to operators via WebSockets with geospatial incident mapping.

---

## Features

- **Live video analysis** — YOLOv8 (nano) processes camera feeds in real time via a Python ML service
- **Multi-incident detection** — Crowd density, suspicious activity, and violence detection
- **Real-time alert streaming** — WebSocket (Socket.io) pushes incidents to the operator dashboard the moment they're detected
- **Geospatial mapping** — Incidents plotted on an interactive map (Leaflet/Mapbox + PostGIS) with location and timestamp
- **Incident timeline** — Chart.js visualizations of incident frequency and type over time
- **Secure access** — JWT authentication for operator accounts
- **Redis caching** — Fast read access for active incident feeds

---

## Architecture

```
┌─────────────────┐     Video feed      ┌──────────────────┐
│  Camera / Feed  │ ─────────────────►  │  Python ML       │
└─────────────────┘                     │  Service (YOLOv8) │
                                        └────────┬─────────┘
                                                 │ Incident event (JSON)
                                        ┌────────▼─────────┐
                                        │  Node.js Backend  │
                                        │  Express + TS     │
                                        │  Socket.io Server │
                                        │  PostgreSQL+PostGIS│
                                        │  Redis cache      │
                                        └────────┬─────────┘
                                                 │ WebSocket
                                        ┌────────▼─────────┐
                                        │  React Dashboard  │
                                        │  TypeScript       │
                                        │  Leaflet maps     │
                                        │  Chart.js         │
                                        └──────────────────┘
```

---

## Tech Stack

### Frontend
| Technology | Purpose |
|-----------|---------|
| React + TypeScript | UI framework |
| Tailwind CSS | Styling |
| Context API | State management |
| Socket.io Client | Real-time incident stream |
| Leaflet / Mapbox | Interactive incident map |
| Chart.js | Incident analytics charts |

### Backend
| Technology | Purpose |
|-----------|---------|
| Node.js + Express (TypeScript) | REST API and WebSocket server |
| Socket.io | Real-time bidirectional events |
| PostgreSQL + PostGIS | Incident storage with geospatial queries |
| Redis | Active feed caching |
| JWT | Operator authentication |

### ML Service
| Technology | Purpose |
|-----------|---------|
| Python | ML service runtime |
| YOLOv8 (Ultralytics) | Object detection on video frames |
| OpenCV | Video capture and frame preprocessing |

---

## Getting Started

### Prerequisites

- Node.js 18+
- Python 3.9+
- PostgreSQL with PostGIS extension enabled
- Redis

### 1. Clone the repo

```bash
git clone https://github.com/VijayShankar10/InciScan---Realtime-Incident-Analysis-System.git
cd InciScan---Realtime-Incident-Analysis-System
```

### 2. Backend setup

```bash
cd server
npm install
```

Create `server/.env`:

```env
DATABASE_URL=postgresql://user:password@localhost:5432/inciscan
REDIS_URL=redis://localhost:6379
JWT_SECRET=your_jwt_secret
PORT=4000
```

Run migrations and start:

```bash
npm run migrate
npm run dev
```

### 3. ML service setup

```bash
cd ml-service
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
python main.py
```

The ML service connects to the backend via WebSocket and starts streaming detections.

### 4. Frontend setup

```bash
cd client
npm install
```

Create `client/.env`:

```env
VITE_API_URL=http://localhost:4000
VITE_WS_URL=ws://localhost:4000
VITE_MAPBOX_TOKEN=your_mapbox_token   # optional, Leaflet works without it
```

```bash
npm run dev
```

Open [http://localhost:5173](http://localhost:5173).

---

## Detection Classes

| Class | Description |
|-------|-------------|
| `crowd_density_high` | Area contains unusually dense crowd |
| `suspicious_behavior` | Loitering, erratic movement patterns |
| `violence_detected` | Physical altercation detected |
| `unattended_object` | Stationary object left in a restricted area |

---

## API Endpoints

| Method | Endpoint | Description |
|--------|---------|-------------|
| `POST` | `/auth/login` | Operator login, returns JWT |
| `GET` | `/incidents` | List all incidents (paginated) |
| `GET` | `/incidents/:id` | Get single incident detail |
| `GET` | `/incidents/live` | Active WebSocket feed |
| `GET` | `/incidents/map` | GeoJSON for map rendering |
| `POST` | `/incidents/:id/resolve` | Mark incident as resolved |

---

## License

GPL-2.0
