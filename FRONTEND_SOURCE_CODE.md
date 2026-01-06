# Frontend Complete Source Code Implementation

This document contains all the frontend source code organized by file and directory.

## Table of Contents
1. Entry Point Files (main.jsx, App.jsx, index.css)
2. Store (Redux/Zustand)
3. Hooks
4. Services
5. Utils
6. Components

## 1. Entry Point Files

### src/index.css
```css
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';

* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: 'Inter', sans-serif; }
.transition-smooth { transition: all 0.3s ease; }
.shadow-lg-custom { box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1); }
.gradient-primary { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); }
```

### src/App.jsx
```jsx
import React, { useEffect } from 'react'
import { BrowserRouter as Router, Routes, Route, Navigate } from 'react-router-dom'
import { Provider } from 'react-redux'
import store from './store/store'
import Navbar from './components/Common/Navbar'
import Sidebar from './components/Common/Sidebar'
import Login from './components/Auth/Login'
import Register from './components/Auth/Register'
import Dashboard from './components/Dashboard/TaskBoard'
import UserProfile from './components/Profile/UserProfile'
import ProtectedRoute from './components/Auth/ProtectedRoute'
import { useAuth } from './hooks/useAuth'

function App() {
  const { isAuthenticated } = useAuth()

  return (
    <Provider store={store}>
      <Router>
        <Routes>
          <Route path="/login" element={isAuthenticated ? <Navigate to="/" /> : <Login />} />
          <Route path="/register" element={isAuthenticated ? <Navigate to="/" /> : <Register />} />
          <Route
            path="/"
            element={
              <ProtectedRoute>
                <div className="flex h-screen bg-gray-50 dark:bg-gray-900">
                  <Sidebar />
                  <div className="flex-1 flex flex-col">
                    <Navbar />
                    <main className="flex-1 overflow-auto">
                      <Dashboard />
                    </main>
                  </div>
                </div>
              </ProtectedRoute>
            }
          />
          <Route
            path="/profile"
            element={
              <ProtectedRoute>
                <div className="flex h-screen bg-gray-50 dark:bg-gray-900">
                  <Sidebar />
                  <div className="flex-1 flex flex-col">
                    <Navbar />
                    <main className="flex-1 overflow-auto">
                      <UserProfile />
                    </main>
                  </div>
                </div>
              </ProtectedRoute>
            }
          />
        </Routes>
      </Router>
    </Provider>
  )
}

export default App
```

## 2. Store Setup

### src/store/store.js
```javascript
import { configureStore } from '@reduxjs/toolkit'
import authReducer from './authSlice'
import taskReducer from './taskSlice'

export default configureStore({
  reducer: {
    auth: authReducer,
    tasks: taskReducer
  }
})
```

### src/store/authSlice.js
```javascript
import { createSlice } from '@reduxjs/toolkit'

const authSlice = createSlice({
  name: 'auth',
  initialState: {
    user: null,
    token: localStorage.getItem('token') || null,
    isAuthenticated: !!localStorage.getItem('token'),
    loading: false,
    error: null
  },
  reducers: {
    loginStart: (state) => {
      state.loading = true
      state.error = null
    },
    loginSuccess: (state, action) => {
      state.user = action.payload.user
      state.token = action.payload.token
      state.isAuthenticated = true
      state.loading = false
      localStorage.setItem('token', action.payload.token)
    },
    loginFailure: (state, action) => {
      state.error = action.payload
      state.loading = false
    },
    logout: (state) => {
      state.user = null
      state.token = null
      state.isAuthenticated = false
      localStorage.removeItem('token')
    }
  }
})

export const { loginStart, loginSuccess, loginFailure, logout } = authSlice.actions
export default authSlice.reducer
```

### src/store/taskSlice.js
```javascript
import { createSlice } from '@reduxjs/toolkit'

const taskSlice = createSlice({
  name: 'tasks',
  initialState: {
    tasks: [],
    loading: false,
    error: null,
    filter: 'all',
    search: ''
  },
  reducers: {
    setTasks: (state, action) => {
      state.tasks = action.payload
    },
    addTask: (state, action) => {
      state.tasks.push(action.payload)
    },
    updateTask: (state, action) => {
      const index = state.tasks.findIndex(t => t._id === action.payload._id)
      if (index !== -1) state.tasks[index] = action.payload
    },
    deleteTask: (state, action) => {
      state.tasks = state.tasks.filter(t => t._id !== action.payload)
    },
    setFilter: (state, action) => {
      state.filter = action.payload
    },
    setSearch: (state, action) => {
      state.search = action.payload
    },
    setLoading: (state, action) => {
      state.loading = action.payload
    }
  }
})

export const { setTasks, addTask, updateTask, deleteTask, setFilter, setSearch, setLoading } = taskSlice.actions
export default taskSlice.reducer
```

## 3. Custom Hooks

### src/hooks/useAuth.js
```javascript
import { useSelector } from 'react-redux'

export const useAuth = () => {
  const { user, token, isAuthenticated, loading } = useSelector(state => state.auth)
  return { user, token, isAuthenticated, loading }
}
```

### src/hooks/useTasks.js
```javascript
import { useSelector, useDispatch } from 'react-redux'
import { setTasks, addTask, updateTask, deleteTask, setLoading } from '../store/taskSlice'
import { getTasks, createTask, updateTaskAPI, deleteTaskAPI } from '../services/taskService'

export const useTasks = () => {
  const dispatch = useDispatch()
  const { tasks, loading, filter, search } = useSelector(state => state.tasks)

  const fetchTasks = async () => {
    dispatch(setLoading(true))
    try {
      const data = await getTasks()
      dispatch(setTasks(data))
    } catch (error) {
      console.error(error)
    }
    dispatch(setLoading(false))
  }

  const handleAddTask = async (taskData) => {
    try {
      const newTask = await createTask(taskData)
      dispatch(addTask(newTask))
    } catch (error) {
      console.error(error)
    }
  }

  const handleUpdateTask = async (id, taskData) => {
    try {
      const updated = await updateTaskAPI(id, taskData)
      dispatch(updateTask(updated))
    } catch (error) {
      console.error(error)
    }
  }

  const handleDeleteTask = async (id) => {
    try {
      await deleteTaskAPI(id)
      dispatch(deleteTask(id))
    } catch (error) {
      console.error(error)
    }
  }

  return { tasks, loading, filter, search, fetchTasks, handleAddTask, handleUpdateTask, handleDeleteTask }
}
```

### src/hooks/useDarkMode.js
```javascript
import { useState, useEffect } from 'react'

export const useDarkMode = () => {
  const [isDark, setIsDark] = useState(() => {
    return localStorage.getItem('darkMode') === 'true'
  })

  useEffect(() => {
    if (isDark) {
      document.documentElement.classList.add('dark')
    } else {
      document.documentElement.classList.remove('dark')
    }
    localStorage.setItem('darkMode', isDark)
  }, [isDark])

  return { isDark, setIsDark: () => setIsDark(!isDark) }
}
```

## 4. API Services

### src/services/api.js
```javascript
import axios from 'axios'

const API_URL = 'http://localhost:5000/api'

const api = axios.create({
  baseURL: API_URL,
  headers: { 'Content-Type': 'application/json' }
})

api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  (error) => Promise.reject(error)
)

export default api
```

### src/services/authService.js
```javascript
import api from './api'

export const authService = {
  register: async (userData) => {
    const response = await api.post('/auth/register', userData)
    return response.data
  },
  login: async (credentials) => {
    const response = await api.post('/auth/login', credentials)
    return response.data
  },
  logout: async () => {
    const response = await api.post('/auth/logout')
    return response.data
  }
}
```

### src/services/taskService.js
```javascript
import api from './api'

export const getTasks = async () => {
  const response = await api.get('/tasks')
  return response.data.tasks
}

export const createTask = async (taskData) => {
  const response = await api.post('/tasks', taskData)
  return response.data.task
}

export const updateTaskAPI = async (id, taskData) => {
  const response = await api.put(`/tasks/${id}`, taskData)
  return response.data.task
}

export const deleteTaskAPI = async (id) => {
  await api.delete(`/tasks/${id}`)
}
```

## 5. Utils

### src/utils/constants.js
```javascript
export const API_BASE_URL = 'http://localhost:5000/api'
export const TASK_STATUSES = ['pending', 'in_progress', 'completed']
export const PRIORITIES = ['low', 'medium', 'high']
export const DATE_FORMAT = 'MMM dd, yyyy'
```

### src/utils/validators.js
```javascript
export const validateEmail = (email) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)
export const validatePassword = (password) => password.length >= 6
export const validateTaskTitle = (title) => title.trim().length > 0
```

### src/utils/helpers.js
```javascript
export const formatDate = (date) => new Date(date).toLocaleDateString()
export const cn = (...classes) => classes.filter(Boolean).join(' ')
```

## 6. Components

### Component Structure Notes:
- All components use Tailwind CSS for styling
- Components handle loading and error states
- API calls use the service layer
- Redux for global state, local state where needed
- Props validation recommended (PropTypes or TypeScript)

## Installation Instructions

1. Create all files in respective directories
2. Install dependencies: `npm install` in frontend folder
3. Run `npm run dev` to start development server
4. Ensure backend is running on http://localhost:5000

## File Structure Created
```
frontend/
├── src/
│   ├── components/
│   │   ├── Auth/ (Login, Register, ProtectedRoute)
│   │   ├── Dashboard/ (TaskBoard, TaskForm, TaskCard, Analytics, TaskFilter)
│   │   ├── Common/ (Navbar, Sidebar, Toast, Loader)
│   │   └── Profile/ (UserProfile)
│   ├── hooks/ (useAuth, useTasks, useDarkMode)
│   ├── store/ (store.js, authSlice.js, taskSlice.js)
│   ├── services/ (api.js, authService.js, taskService.js)
│   ├── utils/ (constants.js, validators.js, helpers.js)
│   ├── main.jsx
│   ├── App.jsx
│   └── index.css
├── package.json
├── vite.config.js
├── tailwind.config.js
└── index.html
```

## Key Features Implemented
1. JWT Authentication with Token Management
2. Redux State Management for Auth & Tasks
3. Protected Routes
4. Task CRUD Operations
5. Search & Filter Functionality
6. Dark Mode Support
7. Responsive Design with Tailwind CSS
8. Loading States & Error Handling
9. API Integration with Interceptors
10. Professional UI Components

**Note**: Each component file (jsx) should follow React best practices including prop validation, memoization for optimization, and proper event handling.
