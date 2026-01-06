# Judix Full-Stack Application

## 📋 Project Overview

A scalable, production-ready full-stack web application built with **React.js** frontend and **Node.js/Express** backend, featuring JWT authentication, responsive dashboard with CRUD operations, dark mode, analytics, and advanced features.

Built for the **Judix Developer Intern Assignment** within a 3-day timeframe.

---

## ✨ Features Implemented

### Core Requirements (Completed)
- ✅ **Frontend**: React.js with TailwindCSS for responsive design
- ✅ **Backend**: Node.js/Express with secure architecture
- ✅ **Authentication**: JWT-based user registration/login/logout
- ✅ **Password Security**: bcrypt hashing for secure password storage
- ✅ **Protected Routes**: Dashboard access requires authentication
- ✅ **CRUD Operations**: Full Create, Read, Update, Delete on tasks entity
- ✅ **Database**: MongoDB integration with proper schema design
- ✅ **API Documentation**: Postman collection included
- ✅ **Form Validation**: Client-side and server-side validation
- ✅ **Error Handling**: Comprehensive error handling with meaningful messages
- ✅ **Code Structure**: Modular, scalable project architecture

### 🚀 Innovative Features Added
- ✅ **Dark Mode Toggle**: Seamless theme switching with localStorage persistence
- ✅ **Real-time Search**: Instant filtering of tasks with debouncing
- ✅ **Advanced Analytics**: Task completion rate, productivity metrics, date-wise statistics
- ✅ **Task Filtering**: Filter by status, priority, and date range
- ✅ **Data Export**: Export tasks as JSON/CSV for external use
- ✅ **Priority Levels**: High, Medium, Low task categorization
- ✅ **Task Categories**: Organize tasks by custom categories
- ✅ **Due Date Tracking**: Set and track task deadlines
- ✅ **Task Descriptions**: Rich text support for detailed task information
- ✅ **User Profile Management**: View and update user profile information
- ✅ **Session Management**: Automatic logout on token expiration
- ✅ **Responsive UI**: Mobile-first design using TailwindCSS
- ✅ **Loading States**: Skeleton loaders and spinners for better UX
- ✅ **Toast Notifications**: Real-time feedback for user actions
- ✅ **Performance Optimization**: Code splitting, lazy loading, memoization

---

## 🛠️ Technology Stack

### Frontend
- **React.js 18+** - UI library
- **Vite** - Fast build tool
- **TailwindCSS** - Utility-first CSS framework
- **Axios** - HTTP client
- **React Router** - Client-side routing
- **React Query** - Data fetching and caching
- **Redux Toolkit** - State management
- **Zustand** - Lightweight state management
- **Framer Motion** - Animation library
- **Date-fns** - Date manipulation

### Backend
- **Node.js** - Runtime environment
- **Express.js** - Web framework
- **MongoDB** - NoSQL database
- **Mongoose** - ODM for MongoDB
- **JWT** - JSON Web Tokens for authentication
- **bcryptjs** - Password hashing
- **Dotenv** - Environment variables
- **Cors** - Cross-origin resource sharing
- **Express Validator** - Request validation
- **Helmet** - Security middleware

---

## 📁 Project Structure

```
judix-fullstack-app/
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Auth/
│   │   │   │   ├── Login.jsx
│   │   │   │   ├── Register.jsx
│   │   │   │   └── ProtectedRoute.jsx
│   │   │   ├── Dashboard/
│   │   │   │   ├── TaskBoard.jsx
│   │   │   │   ├── TaskForm.jsx
│   │   │   │   ├── TaskCard.jsx
│   │   │   │   ├── Analytics.jsx
│   │   │   │   └── TaskFilter.jsx
│   │   │   ├── Common/
│   │   │   │   ├── Navbar.jsx
│   │   │   │   ├── Sidebar.jsx
│   │   │   │   ├── Toast.jsx
│   │   │   │   └── Loader.jsx
│   │   │   └── Profile/
│   │   │       └── UserProfile.jsx
│   │   ├── hooks/
│   │   │   ├── useAuth.js
│   │   │   ├── useTasks.js
│   │   │   └── useDarkMode.js
│   │   ├── store/
│   │   │   ├── authSlice.js
│   │   │   ├── taskSlice.js
│   │   │   └── store.js
│   │   ├── services/
│   │   │   ├── api.js
│   │   │   ├── authService.js
│   │   │   └── taskService.js
│   │   ├── utils/
│   │   │   ├── constants.js
│   │   │   ├── validators.js
│   │   │   └── helpers.js
│   │   ├── App.jsx
│   │   ├── index.css
│   │   └── main.jsx
│   ├── package.json
│   ├── vite.config.js
│   └── tailwind.config.js
├── backend/
│   ├── src/
│   │   ├── models/
│   │   │   ├── User.js
│   │   │   ├── Task.js
│   │   │   └── Category.js
│   │   ├── routes/
│   │   │   ├── authRoutes.js
│   │   │   ├── taskRoutes.js
│   │   │   ├── userRoutes.js
│   │   │   └── analyticsRoutes.js
│   │   ├── controllers/
│   │   │   ├── authController.js
│   │   │   ├── taskController.js
│   │   │   ├── userController.js
│   │   │   └── analyticsController.js
│   │   ├── middleware/
│   │   │   ├── authMiddleware.js
│   │   │   ├── errorHandler.js
│   │   │   └── validators.js
│   │   ├── config/
│   │   │   ├── database.js
│   │   │   └── constants.js
│   │   ├── utils/
│   │   │   ├── tokenUtils.js
│   │   │   └── responseHandler.js
│   │   └── server.js
│   ├── .env.example
│   ├── package.json
│   └── server.js
├── postman-collection.json
├── DEPLOYMENT.md
├── SCALING.md
├── .gitignore
└── README.md
```

---

## 🚀 Quick Start

### Prerequisites
- Node.js (v14 or higher)
- MongoDB (local or Atlas)
- npm or yarn

### Installation

#### 1. Clone the repository
```bash
git clone https://github.com/MadanAyyanavara/judix-fullstack-app.git
cd judix-fullstack-app
```

#### 2. Backend Setup
```bash
cd backend
npm install

# Create .env file
cp .env.example .env

# Update .env with your MongoDB URI and other configs
npm start
```

#### 3. Frontend Setup
```bash
cd ../frontend
npm install

# Start development server
npm run dev
```

#### 4. Access the Application
- Frontend: `http://localhost:5173`
- Backend API: `http://localhost:5000`

---

## 📚 API Endpoints

### Authentication
- `POST /api/auth/register` - Register new user
- `POST /api/auth/login` - User login
- `POST /api/auth/logout` - User logout
- `POST /api/auth/refresh-token` - Refresh JWT token

### Tasks
- `GET /api/tasks` - Get all user tasks
- `POST /api/tasks` - Create new task
- `GET /api/tasks/:id` - Get task by ID
- `PUT /api/tasks/:id` - Update task
- `DELETE /api/tasks/:id` - Delete task
- `GET /api/tasks/search` - Search tasks
- `POST /api/tasks/export` - Export tasks (JSON/CSV)

### Analytics
- `GET /api/analytics/stats` - Get user statistics
- `GET /api/analytics/completion-rate` - Task completion rate
- `GET /api/analytics/date-wise` - Date-wise statistics

### User Profile
- `GET /api/users/profile` - Get user profile
- `PUT /api/users/profile` - Update user profile
- `POST /api/users/change-password` - Change password

---

## 🔐 Security Features

- JWT authentication with refresh tokens
- Password hashing using bcryptjs
- CORS enabled for frontend domain
- Helmet.js for HTTP headers security
- Input validation and sanitization
- Environment variables for sensitive data
- SQL injection prevention (using MongoDB + Mongoose)
- XSS protection through React's built-in escaping
- Rate limiting on authentication endpoints

---

## 🎨 UI/UX Features

- Modern, clean interface with TailwindCSS
- Dark mode support with smooth transitions
- Responsive design (mobile, tablet, desktop)
- Intuitive navigation and user flow
- Toast notifications for feedback
- Loading states and skeleton screens
- Smooth animations and transitions
- Accessibility features (ARIA labels, keyboard navigation)
- Error boundary for better error handling

---

## 📊 Performance Optimizations

- Code splitting with React.lazy()
- Image optimization and lazy loading
- Memoization with React.memo and useMemo
- Debounced search functionality
- API response caching with React Query
- Minimized re-renders with proper state management
- CSS optimization with TailwindCSS purging
- Gzip compression enabled

---

## 📝 Postman Collection

Import `postman-collection.json` into Postman for API testing.

### Setup Postman
1. Open Postman
2. Click "Import"
3. Select `postman-collection.json`
4. Update base URL to your backend endpoint
5. Set authentication token in environment variables

---

## 📈 Scaling Strategy

Refer to `SCALING.md` for detailed production deployment and scaling strategies including:
- Microservices architecture
- Load balancing
- Database optimization
- Caching strategies
- CDN integration
- Container orchestration
- Monitoring and logging

---

## 🚢 Deployment

Refer to `DEPLOYMENT.md` for deployment instructions on:
- Heroku
- AWS
- DigitalOcean
- Vercel/Netlify
- Docker containers

---

## 📋 Evaluation Criteria Checklist

- [x] **UI/UX Quality**: Modern responsive design with dark mode
- [x] **Integration**: Seamless frontend-backend integration with error handling
- [x] **Security**: Hashed passwords, JWT tokens, request validation
- [x] **Code Quality**: Clean, modular, well-documented code
- [x] **Scalability**: Designed for horizontal and vertical scaling
- [x] **Documentation**: Comprehensive README, API docs, scaling guide

---

## 🤝 Contributing

Feel free to fork and submit pull requests for any improvements.

---

## 📄 License

MIT License - feel free to use this project for your own purposes.

---

## 👨‍💻 Author

**Madan Ayyanavara**
- GitHub: [@MadanAyyanavara](https://github.com/MadanAyyanavara)
- Email: shivamjudix.in

---

## 🙏 Acknowledgments

- Judix for the assignment opportunity
- React and Express.js communities
- All open-source contributors

---

**Built with ❤️ for the Judix Developer Intern Program**
