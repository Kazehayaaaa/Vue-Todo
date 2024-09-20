<template>
  <div id="app" class="container">
    <h2>To-Do List with Schedule</h2>

    <!-- Task Input Section -->
    <div class="input-section">
      <input v-model="newTask" @keyup.enter="addTask" placeholder="Add a new task" />
      <input v-model="newTaskDate" type="datetime-local" />

      <!-- Priority Dropdown with Label -->
      <label for="priority">Priority:</label>
      <select id="priority" v-model="newTaskPriority">
        <option value="high">High</option>
        <option value="medium">Medium</option>
        <option value="low">Low</option>
      </select>

      <!-- Category Dropdown with Label -->
      <label for="category">Category:</label>
      <select id="category" v-model="newTaskCategory">
        <option value="personal">Personal</option>
        <option value="work">Work</option>
        <option value="health">Health</option>
        <option value="social">Social</option>
      </select>

      <button @click="addTask">Add Task</button>
    </div>

    <!-- Task List -->
    <ul>
      <li v-for="(task, index) in tasks" :key="index">
        <div v-if="!task.editing">
          <strong>{{ task.name }} ({{ task.category }})</strong><br />
          <small>Priority: {{ task.priority }}</small><br />
          <small>Due: {{ formatDate(task.date) }}</small><br />
          <small v-if="task.completedDate">Completed: {{ formatDate(task.completedDate) }}</small>
        </div>
        <div v-else>
          <input v-model="task.name" placeholder="Edit task" />
          <input v-model="task.date" type="datetime-local" />

          <!-- Priority Dropdown for Editing with Label -->
          <label for="edit-priority">Priority:</label>
          <select id="edit-priority" v-model="task.priority">
            <option value="high">High</option>
            <option value="medium">Medium</option>
            <option value="low">Low</option>
          </select>

          <!-- Category Dropdown for Editing with Label -->
          <label for="edit-category">Category:</label>
          <select id="edit-category" v-model="task.category">
            <option value="personal">Personal</option>
            <option value="work">Work</option>
            <option value="health">Health</option>
            <option value="social">Social</option>
          </select>
        </div>

        <div class="buttons">
          <button v-if="!task.editing" @click="editTask(index)">Edit</button>
          <button v-else @click="saveTask(index)">Save</button>
          <button @click="removeTask(index)">Delete</button>
          <button v-if="!task.completed" @click="markAsCompleted(index)">Mark as Completed</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: '',
      newTaskDate: '',
      newTaskPriority: 'low',
      newTaskCategory: 'personal', // Default category
      tasks: JSON.parse(localStorage.getItem('tasks')) || []
    };
  },
  methods: {
    addTask() {
      if (this.newTask.trim() && this.newTaskDate) {
        this.tasks.push({
          name: this.newTask,
          date: this.newTaskDate,
          priority: this.newTaskPriority,
          category: this.newTaskCategory,
          editing: false,
          completed: false,
          completedDate: null
        });
        this.saveTasks();
        this.resetInputs();
      }
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks();
    },
    editTask(index) {
      this.tasks[index].editing = true;
    },
    saveTask(index) {
      this.tasks[index].editing = false;
      this.saveTasks();
    },
    markAsCompleted(index) {
      this.tasks[index].completed = true;
      this.tasks[index].completedDate = new Date().toISOString();
      this.saveTasks();
    },
    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },
    resetInputs() {
      this.newTask = '';
      this.newTaskDate = '';
      this.newTaskPriority = 'low';
      this.newTaskCategory = 'personal';
    },
    formatDate(value) {
      if (!value) return '';
      const options = { 
        weekday: 'short', year: 'numeric', month: 'short', day: 'numeric', 
        hour: '2-digit', minute: '2-digit' 
      };
      return new Date(value).toLocaleDateString(undefined, options);
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 500px;
  margin: 50px auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.input-section {
  display: flex;
  flex-wrap: wrap;
  margin-bottom: 20px;
}

input, select, label {
  flex: 1;
  margin-bottom: 10px;
  padding: 8px;
  font-size: 1rem;
}

input[type="datetime-local"] {
  margin-left: 10px;
  flex: 2;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin: 10px 0;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: center;
}

.buttons {
  display: flex;
  gap: 10px;
}

button {
  background-color: red;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 5px 10px;
  cursor: pointer;
}

button:nth-child(1) {
  background-color: blue;
}

button:nth-child(2) {
  background-color: green;
}

button:hover {
  opacity: 0.8;
}

@media (max-width: 600px) {
  .container {
    max-width: 100%;
    margin: 20px auto;
  }

  input[type="datetime-local"] {
    margin-left: 0;
    margin-top: 10px;
  }

  .input-section {
    flex-direction: column;
  }

  li {
    flex-direction: column;
    align-items: flex-start;
  }

  .buttons {
    justify-content: flex-start;
    width: 100%;
    margin-top: 10px;
  }

  button {
    width: 100%;
  }
}
</style>
