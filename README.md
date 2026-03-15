# CivicConnect

**AI-powered civic intelligence, grievance redressal, and emergency response platform**

CivicConnect is a full-stack public-service operations system designed for hackathon-scale smart governance use cases. It brings together citizen complaint intake, AI-assisted case understanding, admin-side analytics, CCTV-driven incident escalation, and voice-agent based emergency calling into a single operational workflow.

This is not just a dashboard and not just a chatbot. The project is framed as a unified civic command platform for:

- WhatsApp-based grievance registration
- AI-assisted complaint validation and classification
- map-first administrative monitoring
- real-time CCTV incident escalation
- fall, fight, accident, and suspicious-activity detection workflows
- broader calamity and disaster-response scenarios
- outbound alert calling and collect-details voice-agent flows

## Vision

The core idea behind CivicConnect is to reduce the gap between **incident detection**, **citizen complaint reporting**, and **government response coordination**.

In a realistic city-operations setting, issues can originate from two places:

- a citizen reporting a civic problem through WhatsApp
- a machine-detected emergency event from a camera or monitoring pipeline

CivicConnect is the layer that receives those signals, structures them, enriches them with AI, persists them, surfaces them to operators, and escalates them through voice workflows when rapid action is needed.

## What The Project Solves

Traditional complaint systems are fragmented:

- complaints live in one tool
- maps and analytics live in another
- emergency escalation is manual
- CCTV intelligence is disconnected from public-administration workflows

CivicConnect closes that loop by combining:

- grievance intake
- public-safety incident intelligence
- AI classification
- dashboard-driven decision support
- automated voice escalation

## Solution Pillars

### 1. Citizen Grievance Intake

Citizens can report issues over WhatsApp using text, audio, location, and image inputs. The system guides them through a structured intake flow instead of depending on a rigid form.

### 2. Administrative Command Dashboard

Authorities get a web dashboard for complaint review, map-based inspection, statistics, case details, chatbot-assisted querying, and calling-service monitoring.

### 3. AI Understanding Layer

Gemini is used to:

- validate whether incoming content is a valid complaint
- interpret audio inputs
- validate location context
- classify complaints by category, department, priority, and resolution time
- support the admin chatbot
- assist with spoken-call transcript processing in the calling layer

### 4. CCTV Incident Intelligence

The broader solution integrates with real-time detector pipelines for events such as:

- fall detection
- fight detection
- accident detection
- suspicious activity

The same escalation pattern is positioned for wider calamity-response scenarios as well, which is important to the platform story for emergency governance.

### 5. Voice-Agent Escalation

When urgent intervention is needed, the system connects to a calling agent that can:

- place broadcast alert calls
- speak AI-generated emergency messages
- collect issue details from a caller
- ask for location explicitly
- transcribe responses back into structured records

## End-To-End Story

The project is meant to demonstrate a complete civic operations lifecycle:

1. A citizen reports a problem on WhatsApp, or a detector flags a live incident.
2. CivicConnect structures the incoming signal.
3. AI validates and enriches the event.
4. The event becomes visible on the admin dashboard.
5. If the event is urgent, the voice agent escalates it through automated calling.
6. Operators use the dashboard to review evidence, location, priority, and status.

## High-Level Architecture

```text
Citizens -> WhatsApp Cloud API -------------------------------+
                                                             |
CCTV / Detector Services -> Incident Payloads ---------------+-> CivicConnect Backend -> SQLite
                                                             |          |
                                                             |          +-> Gemini AI
                                                             |          +-> Uploaded evidence storage
                                                             |          +-> Analytics + admin APIs
                                                             |          +-> Chatbot API
                                                             |          +-> Calling-service proxy
                                                             |                     |
                                                             |                     +-> Twilio + voice agent
                                                             |
Admin Dashboard (React + TypeScript) ------------------------+
```

## Major Components

### Backend

The backend is a FastAPI application that acts as the operational core of the system.

Main responsibilities:

- WhatsApp webhook handling
- user-session management
- complaint workflow orchestration
- AI-based validation and classification
- report persistence
- analytics APIs for the dashboard
- image serving
- chatbot APIs
- detector alert ingestion
- calling-service proxy integration

Key backend files:

- `Backend/server.py`
- `Backend/workflow.py`
- `Backend/chatbot.py`
- `Backend/database.py`
- `Backend/models.py`
- `Backend/start_server.py`

### Frontend

The frontend is a React 18 + TypeScript administrative dashboard built with Vite.

It provides a single operator-facing surface for:

- complaint monitoring
- location intelligence
- administrative review
- chatbot-assisted querying
- calling-system visibility

Main UI areas:

- `Home`
- `Map`
- `Complaints`
- `Calls`
- `Chatbot`

Key frontend files:

- `Frontend/src/App.tsx`
- `Frontend/src/components/Navigation.tsx`
- `Frontend/src/components/Heatmap.tsx`
- `Frontend/src/components/StatisticsSection.tsx`
- `Frontend/src/components/ComplaintsTable.tsx`
- `Frontend/src/components/ComplaintDetails.tsx`
- `Frontend/src/components/CallingConsolePage.tsx`
- `Frontend/src/components/ChatbotPage.tsx`
- `Frontend/src/services/api.ts`

### Detector And Calling Stack

The broader workspace also includes companion services that integrate with CivicConnect:

- `testproj/detection_services/fall_fight_detection.py`
- `testproj/detection_services/accident_suspicious_detection.py`
- `testproj/newservice/calling_service`
- `testproj/newservice/detection_service`

These services are important to the overall project narrative:

- the detector services watch live feeds and detect high-risk public-safety events
- the calling service acts as the voice-agent escalation layer
- CivicConnect acts as the command-and-control surface where those machine-generated incidents become visible to administrators

## Citizen Complaint Workflow

The WhatsApp flow is designed as a guided intake conversation.

Typical sequence:

1. The citizen sends a message on WhatsApp.
2. The backend finds or creates an active session.
3. The citizen shares the issue as text or audio.
4. Gemini validates that the content is a genuine complaint.
5. The backend asks for the location.
6. The user responds with text location or a WhatsApp location pin.
7. The backend asks for a supporting image.
8. Gemini validates the image and classifies the complaint.
9. The complaint is stored with a generated report ID.
10. The user receives a structured acknowledgement.

## Incident Detection Workflow

The detector-driven flow is the real-time emergency side of the platform.

Typical sequence:

1. A detector service observes a fight, fall, accident, or suspicious event.
2. The detector verifies the event and prepares a structured alert payload.
3. CivicConnect receives that payload and creates a dashboard-visible record.
4. The event appears in the admin complaint queue and detail view.
5. CivicConnect forwards the alert to the calling layer if escalation is required.
6. A responder receives an outbound voice alert.

This is the part of the project that gives it stronger hackathon optics as a public-safety and civic-intelligence platform rather than a plain complaint form.

## Voice-Agent Workflow

The voice-agent side supports two important patterns.

### Broadcast Alert Calls

Used for fast escalation when an incident has already been understood and only needs to be communicated.

### Collect-Details Calls

Used when the system needs to gather more information from a person through a call. The current flow is designed to:

- ask for the issue
- ask for the exact location
- record the response
- transcribe the answer
- sync the result back into CivicConnect

## Data Model

The backend uses SQLite and currently revolves around three main tables.

### `complaint_reports`

Stores completed or registered complaints and detector-originated incidents.

Important fields:

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

Stores in-progress WhatsApp complaint sessions.

Important fields:

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

Stores data coming back from collect-details voice flows.

Important fields:

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

The backend exposes several route groups.

### WhatsApp And Health

- `GET /`
- `POST /`
- `GET /webhook`
- `POST /webhook`
- `GET /health`

### Reports And Analytics

- `GET /api/reports`
- `GET /api/reports/{report_id}`
- `GET /api/reports/stats`
- `GET /api/reports/by-location`
- `GET /api/filter-options`
- `GET /analytics`

### Chatbot

- `POST /api/chatbot/message`
- `GET /api/chatbot/stats`

### Uploads

- `GET /api/uploads/{filename}`

### Calling And Detection Integration

- `GET /api/calling/status`
- `POST /api/calling/broadcast`
- `POST /api/calling/collect-details`
- `GET /api/calling/collected-records`
- `POST /api/calls/broadcast`
- `POST /api/calls/collect-details`
- `POST /api/detection/alerts/broadcast`
- `POST /api/detection/alerts/collect-details`
- `GET|POST /webhooks/twilio/call-flow`
- `GET|HEAD /audio/{filename}`

## Tech Stack

### Backend

- FastAPI
- Uvicorn
- SQLite
- Requests
- Python-dotenv
- Google Gemini SDKs
- LangChain and LangChain Google GenAI

### Frontend

- React 18
- TypeScript
- Vite
- Mapbox GL
- Recharts
- Lucide React
- Radix UI

### Integrated External Services

- WhatsApp Cloud API
- Google Gemini
- Mapbox
- Twilio

## Local Setup

### Prerequisites

- Python 3.10+
- Node.js 18+
- npm
- Gemini API access
- WhatsApp Cloud API credentials
- Mapbox token for map rendering

## Backend Setup

From `CivicConnect/Backend`:

```bash
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```

Create `Backend/.env` from `Backend/.env.example`:

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

If you are using the calling and detector stack as part of the full demo, also configure:

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

From `CivicConnect/Frontend`:

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

## Full Demo Stack

For the strongest demonstration of the project, the usual stack is:

1. CivicConnect backend on `8000`
2. CivicConnect frontend on `3000`
3. calling service on `5002`
4. one or more detector services on a separate machine or process
5. ngrok or equivalent public exposure for the backend

Typical demo path:

1. A citizen reports a grievance, or a detector flags an emergency.
2. CivicConnect turns that input into a structured operational record.
3. The dashboard reflects the case in real time.
4. The voice agent escalates urgent events through outbound calls.
5. Authorities use the dashboard to review evidence, category, location, priority, and status.

## Frontend To Backend Contract

The frontend expects the backend API base URL to point to `/api`.

Example:

```env
VITE_API_BASE_URL=http://localhost:8000/api
```

## Why This Project Stands Out

CivicConnect is compelling because it combines normally separate systems into one story:

- citizen-facing grievance intake
- AI-powered understanding
- administrative oversight
- CCTV-driven incident intelligence
- public-safety escalation
- voice-agent based response coordination

That makes the project suitable for a strong hackathon narrative around:

- smart cities
- urban safety
- digital governance
- emergency response modernization
- AI-assisted public administration

## Security Notes

- Do not commit `.env` files.
- Do not commit the live SQLite database.
- Do not commit uploaded user images.
- Rotate credentials if they are ever exposed in Git history.

## Additional Documentation

See also:

- `PROJECT_DOCUMENTATION.md`
- `Backend/BACKEND_NON_TECHNICAL_GUIDE.md`

## Summary

CivicConnect is a full-stack civic operations platform that unifies:

- WhatsApp complaint registration
- admin dashboard monitoring
- AI-based complaint and incident understanding
- fall, fight, accident, and suspicious-activity escalation
- calamity-response positioning
- voice-agent calling for alerts and information collection
- analytics, maps, and chatbot-assisted administration
