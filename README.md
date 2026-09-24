# TaskFlow — Task Management Application

A full-stack task management web application built for an internship project. TaskFlow allows users to securely create, manage, organize, search, filter, and track their tasks with real-time updates.


## ✨ Features

### 🔐 Authentication & Authorization

- User registration and login
- JWT-based authentication
- Password hashing using bcrypt
- Protected API routes
- Automatic logout for invalid/expired sessions
- User-specific task isolation

### 📝 Task Management

- Create tasks
- Edit tasks
- Delete tasks
- View task details
- Task title and description
- Due dates
- Status management
- Priority management

### 📊 Task Organization

- Search tasks by title or description
- Filter by status
- Filter by priority
- Sort by:
  - Created date
  - Due date
  - Priority
  - Status
  - Title
- Ascending and descending sorting

### 📈 Dashboard

- Total task count
- Pending tasks
- In Progress tasks
- Completed tasks
- High priority tasks
- Medium priority tasks
- Low priority tasks
- Overdue tasks
- Recent tasks

### ⚡ Real-Time Updates

- Real-time task creation
- Real-time task updates
- Real-time task deletion
- Socket.IO integration
- Updates appear across multiple tabs/devices without refreshing

### 📱 Responsive UI

- Desktop support
- Tablet support
- Mobile support
- Responsive navigation
- Mobile-friendly task cards and forms

### 🛡️ Validation & Security

- Client-side validation
- Server-side validation
- Protected API endpoints
- JWT verification
- bcrypt password hashing
- User-specific database queries
- Environment variables for sensitive configuration

---

## 🛠️ Tech Stack

### Frontend

- React
- Vite
- React Router
- Axios
- Socket.IO Client
- CSS

### Backend

- Node.js
- Express.js
- Socket.IO
- JWT
- bcrypt
- express-validator

### Database

- PostgreSQL
- Neon PostgreSQL for production

### Deployment

- Vercel — Frontend
- Render — Backend
- Neon — PostgreSQL Database

---

## 📂 Project Structure

```text
Taskflow/
├── backend/
│   ├── db/
│   │   └── schema.sql
│   ├── src/
│   │   ├── config/
│   │   │   ├── db.js
│   │   │   └── initDb.js
│   │   ├── controllers/
│   │   │   ├── authController.js
│   │   │   └── taskController.js
│   │   ├── middleware/
│   │   │   ├── auth.js
│   │   │   └── errorHandler.js
│   │   ├── routes/
│   │   │   ├── authRoutes.js
│   │   │   └── taskRoutes.js
│   │   ├── utils/
│   │   │   ├── generateToken.js
│   │   │   └── validators.js
│   │   ├── socket.js
│   │   ├── app.js
│   │   └── server.js
│   ├── .env.example
│   └── package.json
│
├── frontend/
│   ├── src/
│   │   ├── api/
│   │   ├── components/
│   │   ├── context/
│   │   ├── pages/
│   │   ├── styles/
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── .env.example
│   ├── index.html
│   └── package.json
│
└── README.md
