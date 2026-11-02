# Gather — Event & Workshop Registration Platform

A student-friendly full-stack app where organizers manage events and students register for workshops. It demonstrates MongoDB documents, embedded arrays, references, CRUD, filtering, and seat-capacity validation.

## Features

- Create, edit, browse, search, filter, and delete events.
- Save a schedule as an embedded `sessions` array in each event document.
- Save registrations in a separate collection with an `eventId` reference.
- Prevent duplicate email registrations and overbooking.
- View attendee names and registration dates.
- Seed the database with three sample upcoming events.
- Responsive frontend served by Express; no frontend build step is needed.

## Technology and prerequisites

- Node.js 18 or newer
- MongoDB Community Server running locally, or a MongoDB Atlas cluster
- Express, Mongoose, and dotenv

## Setup

### 1. Configure MongoDB

Choose local MongoDB or MongoDB Atlas. For Atlas, create a cluster and database user, allow your IP address in Network Access, and copy the Node.js connection string.

Copy `.env.example` to `.env` in the project folder:

macOS/Linux: `cp .env.example .env`

Windows PowerShell: `Copy-Item .env.example .env`

The sample setting connects to local MongoDB:

```env
MONGODB_URI=mongodb://127.0.0.1:27017/event-workshop-app
PORT=3000
```

For Atlas, set `MONGODB_URI` to your Atlas connection string. Replace the username, password, and cluster host with your own values. URL-encode reserved characters in the password. Keep `.env` private and do not commit it.

### 2. Install dependencies and add sample data

From the project folder, run:

```bash
npm install
npm run seed
```

The seed command adds three sample events only when the events collection is empty. It will not overwrite existing events.

### 3. Start the app

```bash
npm start
```

For development with automatic server restart on compatible Node.js versions:

```bash
npm run dev
```

Open **http://localhost:3000**. Keep the terminal open while using the app.

## MongoDB data model

### `events` collection

An event contains fields such as `title`, `category`, `date`, `capacity`, and `registeredCount`. Its schedule is embedded in the same document:

```json
{
  "title": "Build a Tiny Robot",
  "category": "Technology",
  "capacity": 18,
  "registeredCount": 0,
  "sessions": [
    { "title": "Robot parts and sensors", "startTime": "10:00", "durationMinutes": 45 },
    { "title": "Build and test", "startTime": "11:00", "durationMinutes": 75 }
  ]
}
```

### `registrations` collection

Each registration stores the attendee name and email, registration time, and an `eventId` reference. A unique compound index prevents the same email from registering twice for one event. An atomic conditional update reserves a seat before the registration is saved, preventing simultaneous requests from exceeding capacity.

## API overview

| Method | Route | Purpose |
|---|---|---|
| `GET` | `/api/health` | Check that the server is running |
| `GET` | `/api/events` | List upcoming events; supports `q`, `category`, and `from` filters |
| `POST` | `/api/events` | Create an event |
| `GET` | `/api/events/:id` | Get one event |
| `PUT` | `/api/events/:id` | Update an event |
| `DELETE` | `/api/events/:id` | Delete an event and its registrations |
| `POST` | `/api/events/:id/register` | Register an attendee |
| `GET` | `/api/events/:id/registrations` | List attendee names and registration dates |

## Suggested assignment demo

1. Browse the seeded upcoming events.
2. Create an event with two schedule sessions.
3. Open its details and register with a name and email.
4. Register with the same email again to show duplicate validation.
5. Use different emails until an event is full, then try one more registration to show the capacity check.
6. In MongoDB Atlas Data Explorer or `mongosh`, inspect the `events` and `registrations` collections. Show the embedded `sessions`, referenced `eventId`, and changing `registeredCount`.
7. Edit the event and try lowering capacity below its existing registration count.

Useful shell commands:

```javascript
use event-workshop-app
db.events.find().pretty()
db.registrations.find().pretty()
```

## Educational-demo limitation

This is not a production registration system. It has no organizer login or authorization, email verification, anti-spam protection, or attendee privacy controls. Use fictional demo data only, especially if you deploy it publicly.
