<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Study Task Manager</title>
  <link rel="stylesheet" href="styles.css">
  <style>
    * {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Arial', sans-serif;
  background: linear-gradient(120deg, #00c6ff, #0072ff);
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
  padding: 0;
}

#app {
  width: 100%;
  max-width: 900px;
  text-align: center;
}

h1 {
  font-size: 2.5rem;
  margin-bottom: 20px;
  color: #fff;
}

.add-task-section {
  background-color: rgba(255, 255, 255, 0.2);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  margin-bottom: 30px;
}

input[type="text"], textarea, input[type="datetime-local"] {
  width: 80%;
  padding: 12px;
  margin: 10px 0;
  border-radius: 8px;
  border: 2px solid #ddd;
  background-color: #fff;
  font-size: 1rem;
}

textarea {
  height: 120px;
}

button {
  background-color: #ff4e50;
  color: white;
  border: none;
  padding: 15px 30px;
  border-radius: 30px;
  font-size: 1.1rem;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-top: 10px;
}

button:hover {
  background-color: #ff1c1d;
}

#task-list {
  margin-top: 30px;
  animation: fadeIn 1s ease-in-out;
}

@keyframes fadeIn {
  0% { opacity: 0; }
  100% { opacity: 1; }
}

.task {
  background-color: #fff;
  color: #333;
  border-radius: 10px;
  padding: 20px;
  margin: 15px 0;
  animation: slideIn 0.5s ease;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

@keyframes slideIn {
  0% { transform: translateX(-50%); opacity: 0; }
  100% { transform: translateX(0); opacity: 1; }
}

button.complete {
  background-color: #4CAF50;
  padding: 10px 20px;
  border-radius: 5px;
  color: white;
  cursor: pointer;
}

button.complete:hover {
  background-color: #45a049;
}

button.delete {
  background-color: #f44336;
  padding: 10px 20px;
  border-radius: 5px;
  color: white;
  cursor: pointer;
}

button.delete:hover {
  background-color: #e53935;
}
  </style>
</head>
<body>
  <div id="app">
    <div id="task-manager">
      <h1>Study Task Manager</h1>

      <div class="add-task-section">
        <h3>Add a New Task</h3>
        <input type="text" id="task-title" placeholder="Enter Task Title" required>
        <textarea id="task-desc" placeholder="Enter Task Description"></textarea>
        <input type="datetime-local" id="due-time" required>
        <button onclick="addTask()">Add Task</button>
      </div>

      <div id="task-list">
        <h3>Your Tasks</h3>
        <!-- Tasks will appear here -->
      </div>
    </div>
  </div>

  <script src="script.js"></script>
  <script>
    const taskListElement = document.getElementById("task-list");

function addTask() {
  const title = document.getElementById("task-title").value;
  const description = document.getElementById("task-desc").value;
  const dueTime = document.getElementById("due-time").value;

  if (title && description && dueTime) {
    const task = {
      title: title,
      description: description,
      dueTime: dueTime,
      completed: false
    };

    displayTask(task);
    clearInputFields();
  } else {
    alert("Please fill in all fields!");
  }
}

function displayTask(task) {
  const taskElement = document.createElement("div");
  taskElement.classList.add("task");

  taskElement.innerHTML = `
    <h4>${task.title}</h4>
    <p>${task.description}</p>
    <p><strong>Due:</strong> ${new Date(task.dueTime).toLocaleString()}</p>
    <button class="complete" onclick="markComplete(this)">Mark as Complete</button>
    <button class="delete" onclick="deleteTask(this)">Delete</button>
  `;

  taskListElement.appendChild(taskElement);
}

function markComplete(button) {
  const taskElement = button.parentElement;
  taskElement.style.backgroundColor = "#d4edda"; // Green color
  button.textContent = "Completed";
  button.disabled = true;
}

function deleteTask(button) {
  const taskElement = button.parentElement;
  taskListElement.removeChild(taskElement);
}

function clearInputFields() {
  document.getElementById("task-title").value = "";
  document.getElementById("task-desc").value = "";
  document.getElementById("due-time").value = "";
}
  </script>
</body>
</html>
