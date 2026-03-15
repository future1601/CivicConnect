Stores completed complaints.

- `report_id`
- `session_id`
- `phone_number`
- `description`
- `coordinates`
- `image_path`
- `category`
- `priority`
- `department`
- `resolution_days`
- `status`
- `created_at`
- `updated_at`

### `user_sessions`

Stores in-progress complaint conversations.

Key fields:

- `session_id`
- `phone_number`
- `session_status`
- `complaint_text`
- `coordinates`
- `image_data`
- `created_at`
- `updated_at`
- `expires_at`

### `collected_call_records`

Stores synchronized results from collect-details call workflows.

Key fields:

- `token`
- `flow`
- `call_sid`
- `phone_number`
- `prompt`
- `recording_url`
- `transcript`
- `created_at`
- `completed_at`
- `status`
- `raw_payload`
- `synced_at`

## Main API Surface

The backend exposes several groups of routes.

### WhatsApp Webhook

- `GET /webhook`
- `POST /webhook`
- `GET /`
- `POST /`

These are used for webhook verification and inbound WhatsApp message handling.

### Reports And Analytics

- `GET /api/reports`
- `GET /api/reports/{report_id}`
- `GET /api/reports/stats`
- `GET /api/reports/by-location`
- `GET /api/filter-options`
- `GET /analytics`
- `GET /reports/{phone_number}`

### Chatbot

- `POST /api/chatbot/message`
- `GET /api/chatbot/stats`

### Uploads And Health

- `GET /api/uploads/{filename}`
- `GET /health`

### Calling Integration

- `GET /api/calling/status`
- `POST /api/calls/broadcast`
- `POST /api/calls/collect-details`
- `GET /api/calling/collected-records`

## Tech Stack

### Backend

- FastAPI
- Uvicorn
- SQLite
- Requests
- Python-dotenv
- Google Gemini SDKs
- LangChain / LangChain Google GenAI integration

### Frontend

- React 18
- TypeScript
- Vite
- Lucide React
- Mapbox GL
- Recharts
- Radix UI

## Local Development Setup

### Prerequisites

- Python 3.10+
- Node.js 18+
- npm
- a Gemini API key
- WhatsApp Cloud API credentials if you want to test the citizen workflow
- Mapbox public token for map rendering

## Backend Setup

From the `CivicConnect/Backend` directory:

```bash
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```

Create `Backend/.env` from `Backend/.env.example` and provide values for:

```env
WHATSAPP_TOKEN=...
PHONE_NUMBER_ID=...
VERIFY_TOKEN=...
GEMINI_API_KEY=...
DATABASE_URL=whatsapp_bot.db
HOST=0.0.0.0
PORT=8000
DEBUG=False
```

Calling integration variables:

```env
NEW_CALLING_SERVICE_BASE_URL=http://127.0.0.1:5002
CIVICCONNECT_PUBLIC_BASE_URL=https://your-public-backend-url
NEW_CALLING_SERVICE_PUBLIC_BASE_URL=https://your-public-backend-url
CALLING_SERVICE_TIMEOUT=15
```

Start the backend:

```bash
python server.py
```

or:

```bash
python start_server.py
```

Default backend URL:

```text
http://localhost:8000
```

## Frontend Setup

From the `CivicConnect/Frontend` directory:

```bash
npm install
```

Create `Frontend/.env` from `Frontend/.env.example`:

```env
VITE_API_BASE_URL=http://localhost:8000/api
VITE_MAPBOX_ACCESS_TOKEN=your_mapbox_public_token
VITE_APP_NAME=Admin Dashboard
VITE_APP_VERSION=1.0.0
```

Start the frontend:

```bash
npm run dev
```

Default frontend URL:

```text
http://localhost:3000
```

## Running The Full Local Stack

Run the backend first:

```bash
cd Backend
python server.py
```

Then run the frontend:

```bash
cd Frontend
npm run dev
```

For the full emergency-response flow, also run the separate calling service and point CivicConnect at it with `NEW_CALLING_SERVICE_BASE_URL`.

For the full hackathon demonstration, the typical stack is:

1. CivicConnect backend on `8000`
2. CivicConnect frontend on `3000`
3. calling service on `5002`
4. one or more detector services on a separate machine or process

Typical demonstration flow:

1. A citizen files a complaint on WhatsApp, or a detector identifies a live incident.
2. CivicConnect stores and structures the incident.
3. The admin dashboard immediately reflects the complaint or CCTV event.
4. If escalation is required, CivicConnect forwards the request to the voice agent.
5. Twilio places the outbound call and can collect spoken details back into the platform.

## Frontend To Backend Contract

The frontend expects the backend API base URL to point at `/api`.

Example:

```env
VITE_API_BASE_URL=http://localhost:8000/api
```

The backend itself still exposes some non-`/api` routes for WhatsApp webhooks and health checks.

## File Uploads

Complaint images are stored on disk under backend uploads directories, and the frontend accesses them through:

- `GET /api/uploads/{filename}`

Detector-originated images can also be normalized into backend storage so they appear in the complaint detail view.

## Environment Variables Summary

### Backend

- `WHATSAPP_TOKEN`
- `PHONE_NUMBER_ID`
- `VERIFY_TOKEN`
- `GEMINI_API_KEY`
- `DATABASE_URL`
- `HOST`
- `PORT`
- `DEBUG`
- `NEW_CALLING_SERVICE_BASE_URL`
- `NEW_CALLING_SERVICE_PUBLIC_BASE_URL`
- `CIVICCONNECT_PUBLIC_BASE_URL`
- `CALLING_SERVICE_TIMEOUT`
- `GEMINI_CHAT_MODEL` or `GEMINI_MODEL` for chatbot model override

### Frontend

- `VITE_API_BASE_URL`
- `VITE_MAPBOX_ACCESS_TOKEN`
- `VITE_APP_NAME`
- `VITE_APP_VERSION`

## Security And Operational Notes

- Do not commit `.env` files.
- Do not commit the live SQLite database.
- Do not commit uploaded user images.
- Rotate credentials immediately if they are ever committed to Git history.
- The Mapbox token used in the frontend is a public browser token, but it should still be scoped and managed properly.

## Troubleshooting

### Frontend cannot reach backend

Check:

- backend is running on port `8000`
- `VITE_API_BASE_URL` points to `http://localhost:8000/api`
- CORS is allowing your frontend dev origin

### WhatsApp webhook verification fails

Check:

- `VERIFY_TOKEN`
- public webhook URL configuration in Meta
- backend is publicly reachable through your tunnel or deployment setup

### Maps do not render

Check:

- `VITE_MAPBOX_ACCESS_TOKEN`
- browser console for blocked token or style-loading errors

### Calling routes show as unavailable

Check:

- `NEW_CALLING_SERVICE_BASE_URL`
- the separate calling service is running
- `CIVICCONNECT_PUBLIC_BASE_URL` is configured when Twilio callbacks need a public route

## Additional Documentation

For deeper details, also see:

- `PROJECT_DOCUMENTATION.md`
- `Backend/BACKEND_NON_TECHNICAL_GUIDE.md`

## Why This Matters

CivicConnect is designed as a unified civic operations stack rather than a narrow complaint form.

It brings together:

- citizen grievance intake
- AI-assisted classification and validation
- CCTV-driven incident intelligence
- emergency voice-call escalation
- structured transcript capture
- administrative analytics, mapping, and review

That combination makes it suitable for a hackathon narrative around smart governance, urban safety, responsive public administration, and real-time civic intelligence.

## Summary

CivicConnect is a full-stack governance and emergency-response platform with: