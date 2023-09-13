# Task-Management-System-
The Task Management System is a web-based application that allows users to create, assign, and track tasks within a team. It provides a centralized platform for managing tasks, improving collaboration, and enhancing productivity.
```javascript
// server.js

const express = require('express');
const mongoose = require('mongoose');
const routes = require('./routes');

const app = express();
const PORT = process.env.PORT || 3000;

// Connect to MongoDB
mongoose.connect('mongodb://localhost/task_management_system', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

// Middleware
app.use(express.json());

// Routes
app.use('/api', routes);

// Start the server
app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`);
});
```

```javascript
// routes.js

const express = require('express');
const router = express.Router();
const tasksController = require('./controllers/tasksController');
const authController = require('./controllers/authController');

// Task routes
router.get('/tasks', tasksController.getAllTasks);
router.get('/tasks/:taskId', tasksController.getTaskById);
router.post('/tasks', authController.authenticateUser, tasksController.createTask);
router.put('/tasks/:taskId', authController.authenticateUser, tasksController.updateTask);
router.delete('/tasks/:taskId', authController.authenticateUser, tasksController.deleteTask);

// Authentication routes
router.post('/register', authController.registerUser);
router.post('/login', authController.loginUser);

module.exports = router;
```

```javascript
// controllers/tasksController.js

const Task = require('../models/Task');

// Get all tasks
exports.getAllTasks = async (req, res) => {
  try {
    const tasks = await Task.find({});
    res.json(tasks);
  } catch (error) {
    res.status(500).json({ error: 'An error occurred while retrieving tasks.' });
  }
// Get task by ID
exports.getTaskById = async (req, res) => {
  try {
    const task = await Task.findById(req.params.taskId);
    if (!task) {
      return res.status(404).json({ error: 'Task not found.' });
    }
    res.json(task);
  } catch (error) {
    res.status(500).json({ error: 'An error occurred while retrieving the task.' });
  }
};

// Create a task
exports.createTask = async (req, res) => {
  try {
    const { title, description, assignee } = req.body;
    const task = new Task({ title, description, assignee });
    await task.save();
    res.status(201).json(task);
  } catch (error) {
    res.status(500).json({ error: 'An error occurred while creating the task.' });
  }
};
// Update a task
exports.updateTask = async (req, res) => {
  try {
    const { title, description, assignee } = req.body;
    const task = await Task.findByIdAndUpdate(
      req.params.taskId,
      { title, description, assignee },
      { new: true }
    );
    if (!task) {
      return res.status(404).json({ error: 'Task not found.' });
    }
    res.json(task);
  } catch (error) {
    res.status(500).json({ error: 'An error occurred while updating the task.' });
  }
};

// Delete a task
exports.deleteTask = async (
};
