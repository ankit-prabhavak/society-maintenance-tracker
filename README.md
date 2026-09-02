# Society Maintenance Tracker

A full-stack web application for apartment societies to manage maintenance complaints end-to-end. Residents can raise and track issues with photos, admins triage and resolve them through a clear status workflow, and everyone stays informed via a central notice board and automated email updates.

> **Hosting Notice:** This application is hosted on the Render free tier. The backend server automatically spins down after periods of inactivity. It may take **50 to 60 seconds** for the initial load/authentication request to process while the server wakes up.

---

## Demo Accounts for Review

Use the credentials below to test the active roles in the application, or register a new resident account directly on the signup page.

### Admin Dashboard Access

* **Email:** `tvf@gmail.com`
* **Password:** `ankit1718`

### Resident Portal Access

* **Email:** `ankitabcd1718@gmail.com`
* **Password:** `ankit1718` *(or register a fresh account via the UI)*

---

## Tech Stack

```
React · Node.js · Express · MongoDB · JWT Auth · Cloudinary · Nodemailer

```

| Layer | Technology |
| --- | --- |
| **Frontend** | React 18, React Router v6, plain CSS |
| **Backend** | Node.js, Express |
| **Database** | MongoDB (Mongoose ODM) |
| **Auth** | JWT + bcryptjs |
| **File Upload** | Cloudinary |
| **Email** | Nodemailer (SMTP, e.g., Gmail free tier) |
| **Deployment** | Render.com / Docker |

---

## Features

### Residents

* **Secure Auth:** Register and log in with JWT-based authentication.
* **Raise Complaints:** Submit issues complete with category selection, deep descriptions, and an optional photo upload.
* **Audit History:** View all personal complaints along with a chronological history timeline showing timestamps, acting users, and notes for every single state update.
* **Notice Board:** Browse real-time updates and community guidelines, with critical updates pinned securely to the top.
* **Instant Notifications:** Receive automated emails whenever a complaint status changes or an important announcement goes live.

### Admins

* **Advanced Triage:** Monitor all global community complaints. Filter down records by category, status, priority levels, custom date ranges, or global free-text search.
* **Priority & Workflow Management:** Manually adjust priority levels (`Low` / `Medium` / `High`) and transition tickets through states (`Open` → `In Progress` → `Resolved`).
* **Immutable Resolution:** Resolved issues are locked from further manual changes to protect history records.
* **SLA & Overdue Alerting:** Tickets open beyond a customizable day threshold are dynamically flagged as overdue and automatically bubbled up to the top of the feed.
* **Bulletins:** Broadcast community notices, with optional "Important" flags to pin messages and blast them to all resident email addresses.
* **Metrics Dashboard:** High-level operational view displaying totals broken down by current status, issue categories, priority bands, and active overdue counters.

---

## Project Structure

```text
society-maintenance-tracker/
├── backend/
│   ├── config/
│   ├── controllers/         # (logic currently inline in routes)
│   ├── middleware/
│   │   ├── auth.js          # JWT auth + admin guard
│   │   └── upload.js        # Multer photo upload config
│   ├── models/
│   │   ├── User.js
│   │   ├── Complaint.js
│   │   ├── Notice.js
│   │   └── Settings.js
│   ├── routes/
│   │   ├── auth.js
│   │   ├── complaints.js
│   │   ├── notices.js
│   │   └── dashboard.js
│   ├── utils/
│   │   ├── email.js         # Nodemailer templates
│   │   └── overdue.js       # Overdue detection logic
│   ├── uploads/             # Uploaded complaint photos
│   ├── server.js
│   ├── package.json
│   ├── Dockerfile
│   └── .env.example
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/      # Layout, Badges
│   │   ├── context/         # AuthContext
│   │   ├── pages/           # All route pages
│   │   ├── App.js
│   │   ├── index.js
│   │   └── index.css
│   ├── package.json
│   ├── Dockerfile
│   ├── nginx.conf
│   └── .env.example
├── docker-compose.yml
├── .github/workflows/ci.yml
└── README.md

```

---

## Setup Guide

### Prerequisites

* Node.js 18+
* A running MongoDB instance (local or a cloud-hosted MongoDB Atlas cluster)
* An SMTP Mail server configuration (like a Gmail account utilizing App Passwords)

### 1. Clone & Install Dependencies

```bash
git clone https://github.com/ankit-prabhavak/society-maintenance-tracker.git
cd society-maintenance-tracker

# Setup Backend Environment
cd backend
npm install
cp .env.example .env

# Setup Frontend Environment
cd ../frontend
npm install
cp .env.example .env

```

### 2. Configure Environment Variables

Open your newly created `.env` files and map the keys as detailed in the Environment Variables reference block below.

### 3. Run the Development Environment

```bash
# Terminal 1: Spin up the API server
cd backend
npm run dev          # Nodemon watcher -> target: http://localhost:5000

# Terminal 2: Spin up the UI client
cd frontend
npm start            # Dev server -> target: http://localhost:3000

```

### 4. Admin Seeding Account

A baseline system administrator account is seeded natively into your database on the very first execution boot of the backend runtime. It reads directly from the `ADMIN_EMAIL`, `ADMIN_PASSWORD`, and `ADMIN_NAME` keys passed inside `backend/.env`. Use these values to log in directly via `/login`.

### 5. Windows Operating System Warning

If running locally on a Windows platform using Node 18+, configurations execute natively. If you experience TLS handshaking issues connecting out to MongoDB Atlas on Node 22+, append `--openssl-legacy-provider` into the `start` task definition block inside your `backend/package.json` manifest.

---

## Environment Variables

### Backend Configuration (`backend/.env`)

| Key Name | Purpose / Responsibility | Reference Example |
| --- | --- | --- |
| `PORT` | Target port address for API routing infrastructure. | `5000` |
| `MONGODB_URI` | Dynamic cluster authentication string connection pool. | `mongodb+srv://user:pass@cluster.mongodb.net/db` |
| `JWT_SECRET` | Salt key utilized to cryptographic sign authorization tokens. | `your_long_random_secure_string_here` |
| `FRONTEND_URL` | Explicit address string required for CORS middleware validation. | `http://localhost:3000` |
| `EMAIL_HOST` | Outbound mailing provider SMTP engine endpoint. | `smtp.gmail.com` |
| `EMAIL_PORT` | Port binding assignment for security mail relays. | `587` |
| `EMAIL_USER` | Authenticating dispatch email account address. | `you@gmail.com` |
| `EMAIL_PASS` | Target credential token (Use a Google App Password). | `xxxx xxxx xxxx xxxx` |
| `SOCIETY_NAME` | Template keyword injected into system mail configurations. | `Greenwood Residency` |

> Note: If `EMAIL_USER` remains unmapped, processing flows gracefully jump over outbound email dispatches silently instead of throwing breaking runtime exceptions.

### Frontend Configuration (`frontend/.env`)

| Key Name | Purpose / Responsibility | Reference Example |
| --- | --- | --- |
| `REACT_APP_API_URL` | Target base address pointing out to API routing layers. | `http://localhost:5000/api` |

---

## Database Schema

### `users`

| Property | DataType | Structural Flags / Restrictions |
| --- | --- | --- |
| `name` | String | Required |
| `email` | String | Required, Unique, Lowercase |
| `password` | String | Bcrypt-hashed, protected from API JSON responses |
| `role` | Enum | `resident` | `admin` (Defaults to `resident`) |
| `apartmentNumber` | String | Optional |
| `phone` | String | Optional |
| `timestamps` | Date | Managed automatically via Mongoose engine layer |

### `complaints`

| Property | DataType | Structural Flags / Restrictions |
| --- | --- | --- |
| `title` | String | Required |
| `description` | String | Required |
| `category` | Enum | Plumbing / Electrical / Elevator / Security / Cleaning / Parking / Noise / Internet / Other |
| `status` | Enum | `Open` | `In Progress` | `Resolved` (Defaults to `Open`) |
| `priority` | Enum | `Low` | `Medium` | `High` (Defaults to `Medium`) |
| `photo` | String | Mapped target filename resolving to `/uploads` directory |
| `resident` | ObjectId | Relational mapping link pointing to a `User` model |
| `statusHistory` | Array | Chronological subdocument tracking trail (Schema outlined below) |
| `isOverdue` | Boolean | Read-time calculated flag determined via engine settings |
| `resolvedAt` | Date | Automatically logged when a ticket transitions to `Resolved` |

#### `statusHistory` Subdocument Format

* `status`: Enum (`Open` | `In Progress` | `Resolved`)
* `changedBy`: ObjectId referencing the `User` who modified the state
* `note`: Optional administrative text explanation
* `timestamp`: Date logging field defaulting to system runtime execution time

---

## API Documentation

**Base Gateway Path:** `http://localhost:5000/api`

Authenticated router contexts require a valid bearer authorization element added into your header schemas:

```http
Authorization: Bearer <jwt_token>

```

### Authentication Services

| Method | Route Path | Context | Intended payload behavior |
| --- | --- | --- | --- |
| **POST** | `/auth/register` | Public | Register new resident accounts. |
| **POST** | `/auth/login` | Public | Process credentials. Returns `{ token, user }`. |
| **GET** | `/auth/me` | Auth | Pull structural profile configurations for current user. |

### Complaint Lifecycles

| Method | Route Path | Context | Intended payload behavior |
| --- | --- | --- | --- |
| **POST** | `/complaints` | Resident | File a ticket via `multipart/form-data`. |
| **GET** | `/complaints/my` | Resident | Pull history tickets belonging to current resident context. |
| **GET** | `/complaints` | Admin | Filter global tickets by status, category, date, or query string. |
| **GET** | `/complaints/:id` | Auth | Pull details for a specific ticket. |
| **PATCH** | `/complaints/:id` | Admin | Modify state/priority. Resolved records block modifications. |
| **GET** | `/complaints/settings/overdue-threshold` | Admin | View current tracking configuration settings (in days). |
| **PUT** | `/complaints/settings/overdue-threshold` | Admin | Mutate tracking thresholds via `{ days }` payloads. |

### Notice System

| Method | Route Path | Context | Intended payload behavior |
| --- | --- | --- | --- |
| **GET** | `/notices` | Auth | Pull bulletin records, pinning important metrics first. |
| **POST** | `/notices` | Admin | Publish notice announcements. Important updates fire system emails. |
| **DELETE** | `/notices/:id` | Admin | Purge old announcement items out of live database clusters. |

### Operational Insights

| Method | Route Path | Context | Intended payload behavior |
| --- | --- | --- | --- |
| **GET** | `/dashboard` | Admin | Pull structural layout analytical datasets for core metrics charts. |

---

### Request Execution Blueprints

#### Creating a Complaint

```bash
curl -X POST http://localhost:5000/api/complaints \
  -H "Authorization: Bearer <token>" \
  -F "title=Leaking pipe in kitchen" \
  -F "category=Plumbing" \
  -F "description=Water has been leaking under the sink since this morning" \
  -F "photo=@/path/to/photo.jpg"

```

#### Modifying Status

```bash
curl -X PATCH http://localhost:5000/api/complaints/<id> \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"status": "In Progress", "note": "Plumber scheduled for tomorrow 10am"}'

```

---

## Deployment Instructions

### Render.com Setup

#### Web Service Configuration (Backend API)

1. Select **New Web Service** and bind your source code repository.
2. Define your root compilation target folder pathing as `backend`.
3. Map the runtime installation execution build commands to use `npm install`.
4. Configure your application startup run sequence script array to use `npm start`.
5. Populate the active operational keys matching the definitions inside `.env.example`.
6. Open outbound MongoDB Atlas connection strings to resolve globally (`0.0.0.0/0`).

#### Static Site Configuration (Frontend Client)

1. Select **New Static Site** and map out your repository target paths.
2. Define your compilation target root workspace route configuration folder as `frontend`.
3. Set your operational distribution asset build commands to run `npm install && npm run build`.
4. Bind your operational production deployment directory targets pointing strictly at `build`.
5. Point the environmental variables asset key `REACT_APP_API_URL` to point to your live backend domain gateway string followed by the trailing path `/api`.

---

## Containerization (Docker)

To quickly initialize localized development runtime instances inside standardized container networks, spin up the configured compose infrastructure layout:

```bash
# Populate backend/.env parameters prior to orchestration
docker compose up --build

```

* **Backend Server Gateway:** Available via browser targets at `http://localhost:5000`
* **Frontend Client Gateway:** Available via browser targets at `http://localhost:3000`

---

## CI/CD Verification Track

System testing routines map out dynamically via automated code pipeline parameters written within `.github/workflows/ci.yml`. Commits target branches running merge workflows against `main` automatically pull base dependencies, check architectural syntax structural layers for code validation, and verify stable frontend production asset build states compile without operational warnings.