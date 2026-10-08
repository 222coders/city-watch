# city-watch-fe

Frontend for City Watch, a civic incident-reporting app. Residents report problems in their city — potholes, broken streetlights, graffiti, hazards — and everyone can see open reports on a shared map.

## What it does

- **Submit a report** (`/submit-report`): describe an incident in plain language, or attach a photo (upload or capture straight from the device camera). The backend sends the report to Gemini, which extracts a title, category, urgency, and location, then stores it as a marker. Address autocomplete is powered by Geoapify.
- **Browse the map** (`/map`): every report appears as a marker on a Leaflet map with CARTO basemap tiles. Clicking a marker shows its details, status, and photo (if one was attached) in a popup and sidebar.
- **Home** (`/`): landing page with quick actions, stats, and recent activity.

The app is a Next.js (App Router) client that talks to the [city-watch-be](../city-watch-be) FastAPI service; all report parsing, storage, and image hosting happen there.

### Tech stack

- Next.js 15 (Turbopack) + React 19 + TypeScript
- Tailwind CSS 4 with shadcn/ui (Radix) components
- Leaflet / react-leaflet for the map
- Framer Motion for animation

### Project layout

```
src/
  app/          # Routes: /, /map, /submit-report, /contact, /api, /components-showcase
  components/   # Feature components (map, image upload, camera modal) and ui/ primitives
  contexts/     # MarkersContext — shared marker state
  hooks/        # useCamera
  lib/          # constants (env-backed config) and utils
  types/        # MarkerData and other shared types
```

## Local development

### Prerequisites

- Node.js 20+ and npm
- A running instance of [city-watch-be](../city-watch-be) (defaults to `http://localhost:8000`)
- API keys for [CARTO](https://carto.com/) (basemap tiles) and [Geoapify](https://www.geoapify.com/) (address autocomplete)

### 1. Start the backend

Follow the setup in the city-watch-be README (Postgres + MinIO via Docker, migrations, seed data), then run it:

```bash
uvicorn main:app --reload --port 8000
```

Verify it's up at `http://localhost:8000/health`.

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment variables

```bash
cp .env.example .env
```

| Variable | Purpose |
| --- | --- |
| `NEXT_PUBLIC_CARTO_API_KEY` | Basemap tiles on the map page |
| `NEXT_PUBLIC_GEOAPIFY_API_KEY` | Address autocomplete on the submit-report page |
| `NEXT_PUBLIC_BACKEND_URL` | City Watch API origin, no trailing slash (default `http://localhost:8000`) |

All variables are `NEXT_PUBLIC_*`, so they're inlined into the client bundle at build time — restart the dev server after changing them.

### 4. Run the dev server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

> The camera capture feature requires a secure context. It works on `localhost`, but to test on a phone over your LAN you'll need HTTPS (e.g. `next dev --experimental-https` or an ngrok tunnel).

### Other scripts

| Command | Description |
| --- | --- |
| `npm run build` | Production build (Turbopack) |
| `npm start` | Serve the production build |
| `npm run lint` | Run ESLint |
