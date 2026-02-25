# SecureTask Dashboard

A production-ready full stack web application with authentication, protected dashboard, user profile, and CRUD operations on Tasks.

## Features

- User Registration and Login with JWT authentication
- Protected routes with automatic redirect
- User profile management
- Task CRUD operations (Create, Read, Update, Delete)
- Task search by title
- Filter tasks by status (pending/completed)
- Clean, minimal UI with TailwindCSS

## Tech Stack

### Backend
- Node.js
- Express.js
- MongoDB with Mongoose
- JWT authentication
- bcrypt password hashing
- Express-validator for input validation

### Frontend
- React.js (functional components + hooks)
- React Router v6
- Axios for API calls
- TailwindCSS for styling
- Vite for build tooling

## Folder Structure

```
securetask-dashboard/
├── backend/
│   ├── config/
│   │   └── db.js              # MongoDB connection
│   ├── controllers/
│   │   ├── authController.js  # Auth logic
│   │   ├── userController.js  # User profile logic
│   │   └── taskController.js  # Task CRUD logic
│   ├── middleware/
│   │   └── auth.js            # JWT authentication middleware
│   ├── models/
│   │   ├── User.js            # User schema
│   │   └── Task.js            # Task schema
│   ├── routes/
│   │   ├── authRoutes.js      # Auth API routes
│   │   ├── userRoutes.js      # User API routes
│   │   └── taskRoutes.js      # Task API routes
│   ├── .env.example
│   ├── package.json
│   └── server.js              # Express server entry point
│
└── frontend/
    ├── src/
    │   ├── components/
    │   │   ├── ProtectedRoute.jsx
    │   │   └── LoadingSpinner.jsx
    │   ├── context/
    │   │   └── AuthContext.jsx
    │   ├── pages/
    │   │   ├── LoginPage.jsx
    │   │   ├── RegisterPage.jsx
    │   │   ├── DashboardPage.jsx
    │   │   ├── ProfilePage.jsx
    │   │   └── TasksPage.jsx
    │   ├── services/
    │   │   └── api.js
    │   ├── App.jsx
    │   ├── index.css
    │   └── main.jsx
    ├── index.html
    ├── package.json
    ├── tailwind.config.js
    ├── postcss.config.js
    └── vite.config.js
```

## Setup Instructions

### Prerequisites
- Node.js (v14+)
- MongoDB (local or cloud instance)

### Option 1: Using MongoDB Atlas (Cloud) - Recommended

1. Create a free account at [MongoDB Atlas](https://www.mongodb.com/atlas/database)
2. Create a free cluster and get your connection string
3. Navigate to the project directory:
   
```
   cd securetask-dashboard
   
```
4. Install all dependencies:
   
```
   npm run install:all
   
```
5. Update the `.env` file in backend directory with your Atlas connection string:
   
```
   MONGODB_URI=mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/securetask?retryWrites=true&w=majority
   JWT_SECRET=your-super-secret-jwt-key-change-in-production
   PORT=5000
   NODE_ENV=development
   
```
6. Start both backend and frontend:
   
```
   npm run dev
   
```

### Option 2: Using Local MongoDB

1. Install MongoDB Community Server from [mongodb.com](https://www.mongodb.com/try/download/community)
2. Make sure MongoDB is running (usually on localhost:27017)
3. Navigate to the project directory:
   
```
   cd securetask-dashboard
   
```
4. Install all dependencies:
   
```
   npm run install:all
   
```
5. The `.env` file should already have the default local MongoDB URI:
   
```
   MONGODB_URI=mongodb://localhost:27017/securetask
   
```
6. Start MongoDB service, then:
   
```
   npm run dev
   
```

### Quick Start Commands

After installation, use these commands:

```
bash
cd securetask-dashboard
npm run install:all    # Install all dependencies
npm run seed           # Create default test user (requires MongoDB)
npm run dev            # Start both servers
```

- **Backend:** http://localhost:5000
- **Frontend:** http://localhost:3000

### Default Login Credentials

If you ran the seed script (`npm run seed`), use:
- **Email:** admin@example.com
- **Password:** admin123

Otherwise, register a new account through the UI.

## API Endpoints

### Authentication
| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | /api/auth/register | Register new user | No |
| POST | /api/auth/login | Login user | No |

### User
| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | /api/users/me | Get user profile | Yes |
| PUT | /api/users/me | Update user profile | Yes |

### Tasks
| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | /api/tasks | Get all tasks (with filters) | Yes |
| POST | /api/tasks | Create new task | Yes |
| PUT | /api/tasks/:id | Update task | Yes |
| DELETE | /api/tasks/:id | Delete task | Yes |

### Query Parameters for GET /api/tasks
- `search` - Search tasks by title
- `status` - Filter by status (pending/completed)

## JWT Flow

1. **Registration**: User submits name, email, password. Server creates user and returns JWT token.
2. **Login**: User submits email, password. Server validates and returns JWT token.
3. **Token Storage**: Frontend stores JWT in localStorage.
4. **Protected Requests**: Frontend includes JWT in Authorization header: `Bearer <token>`.
5. **Token Verification**: Backend middleware verifies JWT on protected routes.
6. **Session**: User stays logged in until token expires (30 days).

## Database Schemas

### User
```
javascript
{
  name: String (required, max 50),
  email: String (required, unique),
  password: String (required, min 6, hashed),
  createdAt: Date
}
```

### Task
```javascript
{
  title: String (required, max 100),
  description: String (max 500),
  status: String (enum: 'pending' | 'completed', default: 'pending'),
  userId: ObjectId (ref: User, required),
  createdAt: Date
}
```

## Production Scaling Considerations

### Backend Scaling
- Use PM2 or similar process manager for clustering
- Implement Redis for session/JWT token caching
- Add rate limiting (express-rate-limit)
- Use compression (compression middleware)
- Implement proper logging (winston/morgan)
- Add health check endpoints
- Use environment-based configuration

### Database Scaling
- Use MongoDB Atlas for cloud hosting
- Implement database indexing on frequently queried fields
- Consider read replicas for read-heavy workloads
- Use connection pooling

### Frontend Scaling
- Implement code splitting with React.lazy()
- Add service worker for caching (PWA)
- Use CDN for static assets
- Implement error boundaries
- Add analytics/monitoring

### Security Enhancements
- Implement HTTPS/TLS
- Add CSRF protection
- Use secure HTTP headers (helmet)
- Implement account lockout after failed attempts
- Add email verification
- Implement password reset functionality

## Troubleshooting

### MongoDB Connection Error
If you see `connect ECONNREFUSED 127.0.0.1:27017`, it means MongoDB is not running:
- **Local:** Start MongoDB service or run `mongod`
- **Cloud:** Check your Atlas connection string in `.env`

### Port Already in Use
If port 5000 or 3000 is in use, change the PORT in `.env` or stop the conflicting process.

## License

MIT
