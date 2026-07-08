# Nudge

A gentle, AI-powered wellness companion for building healthy eating habits — without calorie counting, food weighing, or judgment. Nudge replaces harsh tracking with visual portion logging and encouraging, non-restrictive AI feedback.

> **Project status:** Early-stage. Core auth, meal logging, check-ins, and AI chat/feedback are implemented; many features described in the [product requirements doc](docs/product-requirements-doc.md) (Why Wall, Non-Scale Victories, Movement Journey, meal planning, etc.) are planned but not yet built.

## Core Philosophy

Progress over perfection. Small nudges, big changes. Gentle guidance, never judgment.

## Features (Implemented)

- **Firebase Authentication** — email/password and Google sign-in, with user records synced to PostgreSQL on first login
- **Visual Plate Logger** — log meals by placing universal portion shapes (palm, fist, cupped hands, smartphone, ice cream scoop, hockey puck, quarter, shot glass, coffee mug, thumb tip, golf ball, ping pong ball) onto a plate, grouped into protein / vegetable / carb / fat sections — no measuring or calorie counting
- **AI Meal Feedback** — analyzes logged portions and generates encouraging, balance-focused feedback (via Google Gemini) rather than critical or restrictive commentary
- **AI Chat Companion** — conversational nutrition coach with a gentle, non-judgmental system prompt
- **Wellness Check-ins** — quick logging of mood, energy level, and stress level (1–10 scale)
- **User Profiles** — goal selection (general wellness / weight management / bariatric support) and bariatric stage tracking

See [docs/product-requirements-doc.md](docs/product-requirements-doc.md) for the full product vision, including planned Phase 2/3 features, and [docs/brand-identity.md](docs/brand-identity.md) for voice/tone and design language.

## Tech Stack

**Frontend**
- React 19 + TypeScript
- Vite (build/dev server)
- Tailwind CSS
- Firebase Auth (client SDK)

**Backend**
- Node.js + Express 5
- PostgreSQL (via `pg`) for users, meal logs, and check-ins
- Firebase Admin SDK for verifying auth tokens
- Google Gemini API for chat and meal feedback
- ChromaDB / OpenAI embeddings scaffolding for a future RAG pipeline (currently disabled in [server.js](server.js))

**Testing**
- Vitest + React Testing Library

## Project Structure

```
routes/           Express API routes (chat, meals, checkins, users)
middleware/        Firebase auth middleware
services/          Database (Postgres) and vector DB service scaffolding
src/components/    React UI components (plate logger, dashboard, auth, etc.)
src/hooks/         React hooks (plate state, AI feedback)
src/utils/         Meal analysis, portion shape definitions, AI feedback service
src/config/        Firebase client config
tests/             Component, hook, and util tests (Vitest)
docs/              Product requirements, brand identity, and design references
```

## Getting Started

### Prerequisites

- Node.js >= 18
- npm >= 8
- A PostgreSQL database
- A Firebase project (Authentication enabled) — see [FIREBASE_SETUP.md](FIREBASE_SETUP.md)
- A Google Gemini API key
- An OpenAI API key (for future embeddings/RAG work)

### Setup

1. Install dependencies:
   ```bash
   npm install
   ```
2. Copy the environment template and fill in your values:
   ```bash
   cp env.example .env
   ```
   You'll need: `DATABASE_URL`, Firebase frontend config (`VITE_FIREBASE_*`), Firebase Admin SDK credentials (`FIREBASE_PROJECT_ID`, `FIREBASE_CLIENT_EMAIL`, `FIREBASE_PRIVATE_KEY`), `GEMINI_API_KEY`, and `OPENAI_API_KEY`.
3. Follow [FIREBASE_SETUP.md](FIREBASE_SETUP.md) to create your Firebase project and enable Email/Password + Google sign-in.
4. Set up the database schema (see `services/initDB.js` for tables created automatically on server start, plus the Firebase-related columns documented in FIREBASE_SETUP.md).

### Running locally

Run the frontend and backend in separate terminals:

```bash
npm run dev          # Vite dev server (frontend)
npm run dev:backend  # Express server with nodemon (backend, http://localhost:3000)
```

Other scripts:

```bash
npm run build     # Production frontend build
npm run preview   # Preview production build
npm test          # Run tests once (Vitest)
npm run test:watch
npm run test:ui
npm run lint
npm run lint:fix
```

### Verifying the backend

```bash
curl http://localhost:3000/health
curl http://localhost:3000/api/test
```

## API Overview

All routes below (except health/test) require a Firebase ID token via `Authorization: Bearer <token>`.

| Route | Method | Description |
|---|---|---|
| `/health` | GET | Server health check |
| `/api/test` | GET | Basic API sanity check |
| `/api/chat` | POST | Send a message to the AI nutrition coach |
| `/api/meals/log` | POST | Log a meal (portions) |
| `/api/meals/recent` | GET | Get recent meal logs |
| `/api/checkins/log` | POST | Log a wellness check-in (mood, energy, stress) |
| `/api/checkins/recent` | GET | Get recent check-ins |
| `/api/users/profile` | GET | Get current user's profile |
| `/api/users/profile` | PUT | Update goal, name, or bariatric stage |

## Design Principles

- No harsh red error states, no shaming language, no calorie/weight-first metrics
- Rounded, soft UI with a calming palette (sage green, warm cream, muted coral)
- Encouraging copy that celebrates small wins over strict compliance

## Disclaimer

Nudge is **not a medical device** and does not provide medical advice. The bariatric-support features described in the product requirements doc are conceptual — always consult a healthcare provider for medical or nutritional guidance.

## License

ISC
