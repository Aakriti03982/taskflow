# ⚡ TaskFlow — Team Task Manager

A full-stack Java web application for managing team tasks with role-based access control.

**Stack:** Spring Boot 3 · PostgreSQL · React 18 · JWT Auth · Railway

---

## 🚀 Live Deployment on Railway (Step by Step)

### Step 1 — Push to GitHub
```bash
cd taskflow
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/taskflow.git
git push -u origin main
```

### Step 2 — Create Railway Project
1. Go to [railway.app](https://railway.app) → **New Project**
2. Choose **Deploy from GitHub repo** → select your repo
3. Railway auto-detects and builds it

### Step 3 — Add PostgreSQL Database
1. In your Railway project → click **+ New** → **Database** → **PostgreSQL**
2. Railway auto-sets these env vars on your service:
   - `DATABASE_URL`
   - `DATABASE_USERNAME`
   - `DATABASE_PASSWORD`

### Step 4 — Set Environment Variables
Go to your service → **Variables** tab → add:

| Variable | Value |
|---|---|
| `DB_DRIVER` | `org.postgresql.Driver` |
| `DDL_AUTO` | `update` |
| `JWT_SECRET` | any random 32+ char string |
| `PORT` | `8080` (Railway sets this automatically) |

### Step 5 — Deploy
Railway rebuilds automatically. In ~3 minutes your app is live at the Railway URL.

---

## 💻 Run Locally

### Prerequisites
- Java 17+
- Node.js 18+
- Maven 3.8+

### Backend (runs on port 8080, uses H2 in-memory DB)
```bash
cd backend
mvn clean package -DskipTests
java -jar target/taskflow-backend-1.0.0.jar
```

### Frontend (runs on port 5173, proxies API to 8080)
```bash
cd frontend
npm install
npm run dev
```

Open: http://localhost:5173

---

## 📁 Project Structure

```
taskflow/
├── backend/                    # Spring Boot application
│   ├── src/main/java/com/taskflow/
│   │   ├── config/             # Security, CORS, Web config
│   │   ├── controller/         # REST controllers
│   │   ├── dto/                # Request/Response DTOs
│   │   ├── exception/          # Custom exceptions + global handler
│   │   ├── model/              # JPA entities (User, Project, Task, ProjectMember)
│   │   ├── repository/         # Spring Data JPA repositories
│   │   ├── security/           # JWT filter, utils, UserDetailsService
│   │   └── service/            # Business logic
│   └── src/main/resources/
│       ├── static/             # Built React app (served by Spring Boot)
│       └── application.properties
└── frontend/                   # React application
    └── src/
        ├── pages/              # Dashboard, Projects, ProjectDetail, Login, Signup
        ├── components/         # Layout, reusable UI
        ├── context/            # Auth context
        └── api/                # Axios client
```

---

## 🔑 API Endpoints

### Auth
| Method | URL | Auth | Description |
|---|---|---|---|
| POST | `/api/auth/signup` | ❌ | Register |
| POST | `/api/auth/login` | ❌ | Login → returns JWT |
| GET | `/api/auth/me` | ✅ | Current user |
| GET | `/api/auth/users/search?q=` | ✅ | Search users |

### Projects
| Method | URL | Auth | Description |
|---|---|---|---|
| GET | `/api/projects` | ✅ | My projects |
| POST | `/api/projects` | ✅ | Create project (you become Admin) |
| GET | `/api/projects/:id` | ✅ Member | Get project |
| PUT | `/api/projects/:id` | ✅ Admin | Update project |
| DELETE | `/api/projects/:id` | ✅ Admin | Delete project |
| POST | `/api/projects/:id/members` | ✅ Admin | Add member |
| DELETE | `/api/projects/:id/members/:uid` | ✅ Admin | Remove member |

### Tasks
| Method | URL | Auth | Description |
|---|---|---|---|
| GET | `/api/projects/:id/tasks` | ✅ Member | Get all tasks |
| POST | `/api/projects/:id/tasks` | ✅ Admin | Create task |
| GET | `/api/projects/:id/tasks/:tid` | ✅ Member | Get task |
| PUT | `/api/projects/:id/tasks/:tid` | ✅ Admin/Assignee | Update task |
| DELETE | `/api/projects/:id/tasks/:tid` | ✅ Admin | Delete task |

### Dashboard
| Method | URL | Auth | Description |
|---|---|---|---|
| GET | `/api/dashboard` | ✅ | Stats, overdue, activity |

---

## 🛡️ Role-Based Access

**Admin** (project creator):
- Full CRUD on tasks and members
- Can add/remove members
- Can delete the project

**Member** (added by admin):
- View all project tasks
- Update status of assigned tasks only

---

## 🗄️ Database Schema

```
users           → id, name, email, password, created_at
projects        → id, name, description, color, created_by, archived
project_members → id, project_id, user_id, role (ADMIN/MEMBER), joined_at
tasks           → id, title, description, project_id, assigned_to,
                  created_by, status, priority, due_date, completed_at
```
