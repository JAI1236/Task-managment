# TaskFlow — Task Management Application

A full-stack, production-ready task management app built for an internship project.

**Stack:** React (Vite) · Node.js/Express · PostgreSQL · JWT + bcrypt · Socket.IO · Axios

## Features

- Register / Login / Logout with JWT authentication, passwords hashed with bcrypt
- Protected API routes — every task endpoint requires a valid token
- Strict per-user data isolation (a user can only ever see/edit/delete their own tasks)
- Full CRUD on tasks: title, description, status, priority, due date
- Status: `Pending`, `In Progress`, `Completed` · Priority: `Low`, `Medium`, `High`
- Search (title/description), filter (status/priority), sort (date, priority, status, title, order asc/desc)
- Dashboard with live task statistics (total, by status, by priority, overdue)
- Real-time updates across tabs/devices via Socket.IO — no page refresh needed
- Responsive, modern UI (desktop / tablet / mobile)
- Client + server-side validation, loading states, friendly error handling
- Environment-variable-based configuration (no secrets committed)

## Project Structure

```
task-management/
  backend/
    db/schema.sql              # PostgreSQL schema (tables, enums, triggers)
    src/
      config/db.js             # pg connection pool
      config/initDb.js         # applies schema.sql (npm run db:init)
      middleware/auth.js       # JWT verification middleware
      middleware/errorHandler.js
      controllers/authController.js
      controllers/taskController.js
      routes/authRoutes.js
      routes/taskRoutes.js
      utils/generateToken.js
      utils/validators.js      # express-validator rules
      socket.js                # Socket.IO auth + per-user rooms
      app.js                   # Express app (middleware, routes)
      server.js                # HTTP + Socket.IO server entrypoint
    .env.example
    package.json
  frontend/
    src/
      api/                     # axios instance + auth/task API calls
      context/AuthContext.jsx  # auth state, login/register/logout
      context/SocketContext.jsx# Socket.IO client connection
      components/              # Navbar, TaskCard, TaskForm, StatsCards, FilterBar, etc.
      pages/                   # Login, Register, Dashboard, Tasks, NotFound
      styles/index.css         # responsive design system
      App.jsx / main.jsx
    .env.example
    package.json
  README.md
```

## Prerequisites

- Node.js 18+ and npm
- PostgreSQL 14+ (running locally or accessible remotely)

## 1. Database Setup

Create the database and apply the schema.

```bash
# Log into psql (adjust user as needed) and create the database:
psql -U postgres -c "CREATE DATABASE task_management;"
```

## 2. Backend Setup

```bash
cd task-management/backend
npm install
cp .env.example .env
```

Edit `backend/.env` with your PostgreSQL credentials and a strong JWT secret:

```
PORT=5000
NODE_ENV=development
CLIENT_URL=http://localhost:5173

PGHOST=localhost
PGPORT=5432
PGUSER=postgres
PGPASSWORD=postgres
PGDATABASE=task_management

JWT_SECRET=replace_with_a_long_random_string
JWT_EXPIRES_IN=7d
```

Apply the database schema, then start the server:

```bash
npm run db:init     # creates tables/enums/triggers from db/schema.sql
npm run dev          # starts with nodemon on http://localhost:5000
# or: npm start       # plain node, for production
```

Verify it's running: `curl http://localhost:5000/api/health` should return `{"status":"ok",...}`.

## 3. Frontend Setup

Open a second terminal:

```bash
cd task-management/frontend
npm install
cp .env.example .env
```

`frontend/.env` defaults are already correct for local development:

```
VITE_API_URL=http://localhost:5000/api
VITE_SOCKET_URL=http://localhost:5000
```

Start the dev server:

```bash
npm run dev          # starts Vite on http://localhost:5173
```

Open **http://localhost:5173** in your browser, register a new account, and start creating tasks.

## 4. Testing the Real-Time Updates

Open the app in two browser tabs (or two browsers) logged in as the **same user**. Create, edit, or delete a task in one tab — it will appear instantly in the other tab without a refresh, powered by Socket.IO.

## 5. Testing the API Directly (optional)

```bash
# Register
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Jane Doe","email":"jane@example.com","password":"secret123"}'

# Login (copy the returned token)
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"jane@example.com","password":"secret123"}'

# Create a task (replace TOKEN)
curl -X POST http://localhost:5000/api/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer TOKEN" \
  -d '{"title":"Finish report","priority":"High","dueDate":"2026-09-10"}'

# List tasks with search/filter/sort
curl "http://localhost:5000/api/tasks?search=report&status=Pending&sortBy=priority&order=desc" \
  -H "Authorization: Bearer TOKEN"

# Dashboard stats
curl http://localhost:5000/api/tasks/stats -H "Authorization: Bearer TOKEN"
```

## API Reference

| Method | Endpoint             | Auth | Description                                  |
|--------|-----------------------|------|-----------------------------------------------|
| POST   | /api/auth/register     | No   | Create an account, returns user + JWT         |
| POST   | /api/auth/login        | No   | Log in, returns user + JWT                    |
| GET    | /api/auth/me           | Yes  | Get the current user's profile                |
| GET    | /api/tasks             | Yes  | List current user's tasks (search/filter/sort/pagination via query params) |
| GET    | /api/tasks/stats       | Yes  | Task statistics for the current user          |
| GET    | /api/tasks/:id         | Yes  | Get one task                                  |
| POST   | /api/tasks             | Yes  | Create a task                                 |
| PUT    | /api/tasks/:id         | Yes  | Update a task                                 |
| DELETE | /api/tasks/:id         | Yes  | Delete a task                                 |

Query params for `GET /api/tasks`: `search`, `status`, `priority`, `sortBy` (`createdAt`|`dueDate`|`priority`|`status`|`title`), `order` (`asc`|`desc`), `page`, `limit`.

## Deployment Notes

- **Database**: Provision a managed PostgreSQL instance (e.g. Neon, Supabase, Render, RDS). Run `psql <connection-string> -f backend/db/schema.sql` once, or set `DATABASE_URL` and run `npm run db:init`.
- **Backend**: Deploy to any Node host (Render, Railway, Fly.io, EC2, etc.). Set `DATABASE_URL` (or `PG*` vars), `JWT_SECRET`, `CLIENT_URL` (your deployed frontend origin), and `PORT` as environment variables. Socket.IO runs on the same HTTP server, so no extra configuration is needed beyond allowing WebSocket connections on your host.
- **Frontend**: Build with `npm run build` (outputs to `frontend/dist`) and deploy the static files to Vercel, Netlify, or any static host. Set `VITE_API_URL` and `VITE_SOCKET_URL` to your deployed backend's URL at build time.
- Always use HTTPS in production and rotate `JWT_SECRET` to a long, random value — never reuse the example value.

## Security Notes

- Passwords are hashed with bcrypt (10 salt rounds), never stored in plain text.
- JWTs are verified on every protected request and Socket.IO connection.
- All task queries are scoped with `WHERE user_id = $1`, so no user can read or modify another user's data even by guessing IDs.
- Input is validated server-side with `express-validator` in addition to client-side checks.
