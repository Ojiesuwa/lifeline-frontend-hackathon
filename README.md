# LifeLine

LifeLine is a front-end emergency response prototype built with Next.js. It simulates a rescue coordination flow where a user describes an emergency, triggers an AI dispatch flow, receives incoming responder calls, and views a final operation summary after the call cycle ends.

This project is focused on the UI and event-driven interaction layer rather than full production dispatch infrastructure. The experience is designed to feel like a polished emergency call dashboard with live status updates, animated call screens, and an ElevenLabs voice session hook.

## What the app does

The app follows a realistic emergency coordination flow:

1. The user enters an emergency description into the textbox.
2. The app opens a WebSocket connection and sends a START_INCIDENT message.
3. The right panel shows live agent operations as messages are updated.
4. The system emits an incoming call state when a responder call is triggered.
5. The user can accept or decline the call.
6. If accepted, the browser requests microphone access and the ElevenLabs conversation session begins.
7. Once the operation completes, the UI swaps to a summary view with picked-up, declined, and failed contacts.

The underlying idea is a demo of an AI-assisted emergency coordination system that can route calls to human responders while surfacing operational progress in a clean interface.

## Tech stack

- Next.js 16
- React 19
- TypeScript
- WebSockets with ws
- ElevenLabs client for voice session handling
- Framer Motion for animations
- lucide-react for icons
- CSS custom styling for the phone UI

## Current project structure

```text
.
├── public/
│   ├── call.mp3
│   └── phone.png
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   └── ws/
│   │   │       └── server.ts
│   │   ├── globals.css
│   │   ├── layout.tsx
│   │   ├── page.css
│   │   └── page.tsx
│   └── components/
│       ├── ActiveCall/
│       │   ├── ActiveCall.css
│       │   └── ActiveCall.tsx
│       ├── BottomGlow/
│       │   ├── BottomGlow.module.css
│       │   └── BottomGlow.tsx
│       ├── ClockUI/
│       │   ├── ClockUI.css
│       │   └── ClockUI.tsx
│       └── IncomingCall/
│           ├── IncomingCall.css
│           └── IncomingCall.tsx
├── AGENTS.md
├── CLAUDE.md
├── eslint.config.mjs
├── next-env.d.ts
├── next.config.ts
├── package.json
├── README.md
├── tsconfig.json
└── public/
```

## Main application flow

### 1. Emergency input screen

The landing screen is controlled by src/app/page.tsx and shows a phone-like layout with an emergency text area. The default message is a realistic asthma emergency example.

The user can:

- edit the emergency text
- trigger the dispatch flow
- watch the live operation log update

### 2. Agent lifecycle and WebSocket communication

The page keeps a WebSocket connection open to a socket at ws://localhost:4000. It listens for event types such as:

- AGENT_STATUS
- CALL_STARTED
- AGENT_ACTIVE
- START_CALL
- OPERATION_COMPLETE

It sends events like:

- START_INCIDENT
- CALL_ACCEPTED
- CALL_DECLINED
- CALL_ENDED

This creates a live event-driven loop that simulates a backend emergency responder orchestration system.

### 3. Incoming call UI

When a START_CALL event arrives, the app switches into an incoming-call screen. The component in src/components/IncomingCall/IncomingCall.tsx plays a ringtone and shows animated rings, caller information, and accept/decline controls.

### 4. Active voice call UI

After acceptance, the app moves to the active-call view in src/components/ActiveCall/ActiveCall.tsx. It shows:

- connected status
- caller label
- timing display
- animated audio bars
- end-call action

### 5. Operation summary panel

When the backend reports OPERATION_COMPLETE, the app swaps the live log for a final summary panel. It includes:

- total people called
- total call duration
- picked-up contacts
- declined contacts
- failed contacts
- a reset action to return to the original form

This is one of the more important UX additions in the current version of the project and is implemented in src/app/page.tsx.

## WebSocket server

The project includes a custom WebSocket server in src/app/api/ws/server.ts. It creates a Node HTTP server, upgrades request connections for /api/ws, and sends a CONNECTED message to clients when the socket is opened.

Current behavior:

- logs connection events
- emits CONNECTED on open
- echoes incoming payloads back as RESPONSE messages
- logs connection close events

This file acts as a backend reference and test harness for the front-end flow, but it is not a full production emergency dispatch service.

## Important runtime note

The front-end currently connects to a WebSocket at ws://localhost:4000, while the Next.js app itself runs on port 3000. In other words, this project is designed to work with a separate WebSocket service or mock backend alongside the Next.js app.

This is a key point to keep in mind when running the project locally.

## Setup

Install dependencies:

```bash
npm install
```

Start the app:

```bash
npm run dev
```

Then open the app in a browser:

```text
http://localhost:3000
```

If you want the WebSocket mock backend to participate in the flow, run the custom socket service or a separate backend that matches the client’s expected event protocol.

## Scripts

```bash
npm run dev     # start the Next.js development server
npm run build   # create a production build
npm run start   # run the production build
npm run lint    # lint the project with ESLint
```

## Current status

This repo is best described as a polished emergency-response demo UI with a live event-driven call flow. It demonstrates:

- emergency intake
- AI agent status tracking
- call acceptance and rejection
- voice-session integration
- operation completion summaries
- animated mobile/phone UI interactions

It is not yet a full production-grade emergency dispatch platform, but it is a strong front-end prototype for a voice-based emergency orchestration experience.

## Summary

LifeLine is a concept app for an AI-powered emergency response assistant. The user-facing experience is the focus: it feels like a real emergency operation dashboard with an incoming call flow, voice session integration, and a final success/failure summary. The repository is a front-end prototype that models the workflow and UI of a rescue coordination platform without implementing a complete backend service.
