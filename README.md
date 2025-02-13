
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>School Study Task Manager</title>
  <link rel="stylesheet" href="styles.css">
  <style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Arial', sans-serif;
  background: linear-gradient(120deg, #4CAF50, #81C784);
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
  flex-direction: column;
  transition: background 0.3s ease, color 0.3s ease;
}

body.dark-mode {
  background: linear-gradient(120deg, #2C3E50, #34495E);
  color: #fff;
}

header {
  background: #2E7D32;
  width: 100%;
  text-align: center;
  padding: 20px;
  font-size: 1.8rem;
  font-weight: bold;
}

#dark-mode-toggle {
  position: absolute;
  top: 20px;
  right: 20px;
  background: #2E7D32;
  border: none;
  color: white;
  padding: 10px 20px;
  border-radius: 20px;
  cursor: pointer;
}

#dark-mode-toggle:hover {
  background: #1B5E20;
}

#app {
  width: 100%;
  max-width: 900px;
  text-align: center;
}

.add-task-section {
  background-color: rgba(255, 255, 255, 0.2);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  margin-bottom: 30px;
}

input, textarea, select {
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
  background-color: #2E7D32;
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
  background-color: #1B5E20;
}

#task-list .task {
  background: white;
  color: black;
  border-radius: 8px;
  padding: 15px;
  margin: 10px 0;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  opacity: 1;
  transform: scale(1);
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.complete {
  background-color: #28a745;
  transform: scale(1.05);
  animation: pulse 1s infinite;
}

.edit {
  background-color: #FFC107;
}

.delete {
  background-color: #DC3545;
}

@keyframes pulse {
  0% {
    transform: scale(1.05);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1.05);
  }
}

button:focus {
  outline: none;
}

button:hover {
  background-color: #1B5E20;
}

button:active {
  transform: scale(0.98);
}

.progress-bar {
  width: 100%;
  height: 10px;
  background-color: #ddd;
  border-radius: 5px;
  margin-top: 10px;
}

.progress {
  height: 100%;
  background-color: #4CAF50;
  border-radius: 5px;
}

#task-stats {
  margin: 20px 0;
  font-size: 1.2rem;
}
  
  </style>
</head>
<body>
  <div id="app">
    <header>
      <h1>🏫 School Study Task Manager</h1>
      <button id="dark-mode-toggle">🌙 Dark Mode</button>
    </header>

    <div id="task-manager">
      <div class="add-task-section">
        <h3>📝 Add a New Task</h3>
        <input type="text" id="student-name" placeholder="Enter Student Name" required>
        <input type="text" id="task-title" placeholder="Enter Task Title" required>
        <select id="subject" required>
          <option value="">Select Subject</option>
          <option value="Math">Math</option>
          <option value="Science">Science</option>
          <option value="English">English</option>
          <option value="History">History</option>
          <option value="Other">Other</option>
        </select>
        <select id="priority" required>
          <option value="">Select Priority</option>
          <option value="High">High</option>
          <option value="Medium">Medium</option>
          <option value="Low">Low</option>
        </select>
        <textarea id="task-desc" placeholder="Enter Task Description"></textarea>
        <input type="datetime-local" id="due-time" required>
        <button id="submit-btn">Add Task</button>
        <button id="update-btn" style="display:none;">Update Task</button>
      </div>

      <div class="filters">
        <input type="text" id="search-bar" placeholder="Search by Student Name or Subject">
        <button id="show-all">Show All</button>
        <button id="show-completed">Completed</button>
        <button id="show-pending">Pending</button>
        <button id="delete-all">Delete All</button>
        <button id="export-tasks">Export Tasks</button>
        <select id="sort-by">
          <option value="">Sort By</option>
          <option value="dueDate">Due Date</option>
          <option value="priority">Priority</option>
          <option value="subject">Subject</option>
        </select>
      </div>

      <div id="task-stats">
        <p>Total Tasks: <span id="total-tasks">0</span></p>
        <p>Completed Tasks: <span id="completed-tasks">0</span></p>
        <p>Pending Tasks: <span id="pending-tasks">0</span></p>
      </div>

      <div id="task-list">
        <h3>📋 Your Tasks</h3>
      </div>
    </div>
  </div>

  <script src="script.js"></script>
  <script>
    let db;

// IndexedDB Setup
const request = indexedDB.open("SchoolTaskDB", 1);

request.onupgradeneeded = function(event) {
  let db = event.target.result;
  if (!db.objectStoreNames.contains("tasks")) {
    db.createObjectStore("tasks", { keyPath: "id" });
  }
};

request.onsuccess = function(event) {
  db = event.target.result;
  loadTasks();
};

request.onerror = function() {
  console.error("Error opening IndexedDB.");
};

// Dark Mode Toggle
document.getElementById("dark-mode-toggle").addEventListener("click", function() {
  document.body.classList.toggle("dark-mode");
});

// Add Task
document.getElementById("submit-btn").addEventListener("click", function() {
  const studentName = document.getElementById("student-name").value;
  const title = document.getElementById("task-title").value;
  const subject = document.getElementById("subject").value;
  const priority = document.getElementById("priority").value;
  const description = document.getElementById("task-desc").value;
  const dueTime = document.getElementById("due-time").value;

  if (studentName && title && subject && priority && description && dueTime) {
    const task = {
      id: Date.now(),
      studentName,
      title,
      subject,
      priority,
      description,
      dueTime,
      completed: false
    };

    let transaction = db.transaction(["tasks"], "readwrite");
    let store = transaction.objectStore("tasks");
    store.add(task);

    transaction.oncomplete = function() {
      loadTasks();
      clearInputFields();
    };
  } else {
    alert("Please fill in all fields!");
  }
});

// Load Tasks
function loadTasks() {
  let transaction = db.transaction(["tasks"], "readonly");
  let store = transaction.objectStore("tasks");
  let request = store.getAll();

  request.onsuccess = function() {
    let tasks = request.result;
    updateTaskStats(tasks);
    displayTasks(tasks);
  };
}

// Update Task Stats
function updateTaskStats(tasks) {
  const totalTasks = tasks.length;
  const completedTasks = tasks.filter(task => task.completed).length;
  const pendingTasks = totalTasks - completedTasks;

  document.getElementById("total-tasks").textContent = totalTasks;
  document.getElementById("completed-tasks").textContent = completedTasks;
  document.getElementById("pending-tasks").textContent = pendingTasks;
}

// Display Tasks
function displayTasks(tasks) {
  const taskListElement = document.getElementById("task-list");
  taskListElement.innerHTML = "";
  tasks.forEach(task => {
    const taskElement = document.createElement("div");
    taskElement.classList.add("task");
    if (task.completed) taskElement.classList.add("complete");

    taskElement.dataset.id = task.id;
    taskElement.innerHTML = `
      <h4>👤 ${task.studentName}</h4>
      <h4>${task.title}</h4>
      <p><strong>Subject:</strong> ${task.subject}</p>
      <p><strong>Priority:</strong> ${task.priority}</p>
      <p>${task.description}</p>
      <p><strong>Due:</strong> ${new Date(task.dueTime).toLocaleString()}</p>
      <div class="progress-bar">
        <div class="progress" style="width: ${task.completed ? '100%' : '0%'}"></div>
      </div>
      <button class="complete" onclick="markComplete(${task.id})">Mark as Complete</button>
      <button class="edit" onclick="editTask(${task.id})">Edit</button>
      <button class="delete" onclick="deleteTask(${task.id})">Delete</button>
    `;
    taskListElement.appendChild(taskElement);
  });
}

// Mark as Complete
function markComplete(taskId) {
  let transaction = db.transaction(["tasks"], "readwrite");
  let store = transaction.objectStore("tasks");
  let request = store.get(taskId);

  request.onsuccess = function() {
    let task = request.result;
    task.completed = true;
    store.put(task);
    loadTasks();
  };
}

// Edit Task
function editTask(taskId) {
  let transaction = db.transaction(["tasks"], "readonly");
  let store = transaction.objectStore("tasks");
  let request = store.get(taskId);

  request.onsuccess = function() {
    let task = request.result;
    document.getElementById("student-name").value = task.studentName;
    document.getElementById("task-title").value = task.title;
    document.getElementById("subject").value = task.subject;
    document.getElementById("priority").value = task.priority;
    document.getElementById("task-desc").value = task.description;
    document.getElementById("due-time").value = task.dueTime;

    document.getElementById("submit-btn").style.display = "none";
    document.getElementById("update-btn").style.display = "block";

    document.getElementById("update-btn").onclick = function() {
      task.studentName = document.getElementById("student-name").value;
      task.title = document.getElementById("task-title").value;
      task.subject = document.getElementById("subject").value;
      task.priority = document.getElementById("priority").value;
      task.description = document.getElementById("task-desc").value;
      task.dueTime = document.getElementById("due-time").value;

      let updateTransaction = db.transaction(["tasks"], "readwrite");
      let updateStore = updateTransaction.objectStore("tasks");
      updateStore.put(task);

      updateTransaction.oncomplete = function() {
        loadTasks();
        document.getElementById("submit-btn").style.display = "block";
        document.getElementById("update-btn").style.display = "none";
        clearInputFields();
      };
    };
  };
}

// Delete Task
function deleteTask(taskId) {
  let transaction = db.transaction(["tasks"], "readwrite");
  let store = transaction.objectStore("tasks");
  store.delete(taskId);

  transaction.oncomplete = function() {
    loadTasks();
  };
}

// Delete All Tasks
document.getElementById("delete-all").addEventListener("click", function() {
  let transaction = db.transaction(["tasks"], "readwrite");
  let store = transaction.objectStore("tasks");
  store.clear();

  transaction.oncomplete = function() {
    loadTasks();
  };
});

// Export Tasks
document.getElementById("export-tasks").addEventListener("click", function() {
  let transaction = db.transaction(["tasks"], "readonly");
  let store = transaction.objectStore("tasks");
  let request = store.getAll();

  request.onsuccess = function() {
    let tasks = request.result;
    let csvContent = "data:text/csv;charset=utf-8,";
    csvContent += "Student Name,Task Title,Subject,Priority,Description,Due Time,Completed\n";
    tasks.forEach(task => {
      csvContent += `${task.studentName},${task.title},${task.subject},${task.priority},${task.description},${task.dueTime},${task.completed}\n`;
    });

    let encodedUri = encodeURI(csvContent);
    let link = document.createElement("a");
    link.setAttribute("href", encodedUri);
    link.setAttribute("download", "tasks.csv");
    document.body.appendChild(link);
    link.click();
  };
});

// Sort Tasks
document.getElementById("sort-by").addEventListener("change", function() {
  let sortBy = this.value;
  let transaction = db.transaction(["tasks"], "readonly");
  let store = transaction.objectStore("tasks");
  let request = store.getAll();

  request.onsuccess = function() {
    let tasks = request.result;
    if (sortBy === "dueDate") {
      tasks.sort((a, b) => new Date(a.dueTime) - new Date(b.dueTime));
    } else if (sortBy === "priority") {
      const priorityOrder = { High: 1, Medium: 2, Low: 3 };
      tasks.sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
    } else if (sortBy === "subject") {
      tasks.sort((a, b) => a.subject.localeCompare(b.subject));
    }
    displayTasks(tasks);
  };
});

// Clear Input Fields
function clearInputFields() {
  document.getElementById("student-name").value = "";
  document.getElementById("task-title").value = "";
  document.getElementById("subject").value = "";
  document.getElementById("priority").value = "";
  document.getElementById("task-desc").value = "";
  document.getElementById("due-time").value = "";
    }
  </script>
</body>
</html>
