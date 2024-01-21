<template>
   <div :class="{ 'dark': isDarkMode }" :style="{ backgroundColor: isDarkMode ? 'black' : '' }">
      <h1 class="text-4xl font-bold mb-4">Task List</h1>
      <div class="flex flex-col md:flex-row items-center space-y-4 md:space-y-0 md:space-x-2">
         <input
            type="text"
            v-model="newTask.title"
            class="w-full md:w-96 lg:w-1/3 xl:w-1/4 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
            placeholder="Task Title"
            />
         <input
            type="text"
            v-model="newTask.description"
            class="w-full md:w-96 lg:w-1/3 xl:w-1/4 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 p-2.5 mt-2 md:mt-0 md:ml-2 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
            placeholder="Task Description"
            />
         <button @click="addTask" class="w-full md:w-60 lg:w-32 xl:w-40 bg-blue-500 text-white py-3 rounded-lg mt-2 md:mt-0">
         Add Task
         </button>
         <!-- Replace the button with a toggle input -->
         <div class="ml-auto flex items-center">
            <input
               type="checkbox"
               @click="toggleDarkMode"
               class="px-3 py-1 rounded-md text-white bg-gray-800"
               :checked="isDarkMode"
               />
            <span>Dark Mode</span>
         </div>
      </div>
      <ul class="space-y-4">
         <li v-for="task in tasks" :key="task.id" class="bg-white rounded-lg p-4 shadow-md">
            <div class="flex flex-col md:flex-row items-center box-data">
               <div class="flex items-center space-x-2">
                  <input type="checkbox" v-model="task.completed" class="mr-2" />
                  <span :class="{ 'text-black': isDarkMode }">{{ task.name }}</span>
               </div>
               <div class="text-gray-600 text-sm md:ml-2">
                  {{ task.description }}
               </div>
               <div class="flex-grow md:flex-grow-1"></div>
               <div class="flex items-center space-x-4 mt-2 md:mt-0">
                  <p class="text-gray-500 text-sm text-center">Created Date:{{ task.createdTime }}</p>
                  <div class="ml-auto"></div>
                  <button @click="startEditing(task.id)" class="bg-blue-500 text-white px-4 py-2 rounded-md">Edit</button>
                  <button @click="confirmDeleteTask(task.id)" class="bg-red-500 text-white px-4 py-2 rounded-md">Delete</button>
               </div>
            </div>
            <!-- Edit form -->
            <div v-if="editingTaskId === task.id" class="mt-4 md:mt-0 md:ml-8 md:flex-grow">
               <label>Title:</label>
               <input v-model="editedTaskName" class="w-full bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" />
               <label class="mt-2">Description:</label>
               <textarea v-model="editedTaskDescription" class="w-full bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"></textarea>
               <button @click="finishEditing" class="mt-2 bg-green-500 text-white px-4 py-2 rounded-md">Save Changes</button>
            </div>
         </li>
      </ul>
   </div>
</template>
<style>
@import 'https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.css';

</style>
<script>


export default {
  data() {
    return {
      tasks: [],
      newTask: {
        title: '',
        description: '',
      },
      editingTaskId: null,
      editedTaskName: '',
      editedTaskDescription: '',
      isDarkMode: false,
    };
  },
  methods: {
    addTask() {
      const currentTime = new Date();
      const formattedTime = currentTime.toLocaleString();
      this.tasks.push({
        id: Math.random().toString(36).substring(7),
        name: this.newTask.title,
        description: this.newTask.description,
        completed: false,
        createdTime: formattedTime,
      });
      this.newTask.title = '';
      this.newTask.description = '';
      this.saveTasksToLocalStorage();
    },
    deleteTask(id) {
      this.tasks = this.tasks.filter((task) => task.id !== id);
      this.saveTasksToLocalStorage();
    },
    startEditing(id) {
      this.editingTaskId = id;
      const editedTask = this.tasks.find((task) => task.id === id);
      this.editedTaskName = editedTask.name;
      this.editedTaskDescription = editedTask.description || ''; // Add description property if not present
    },
    finishEditing() {
      const editedTask = this.tasks.find((task) => task.id === this.editingTaskId);
      editedTask.name = this.editedTaskName;
      editedTask.description = this.editedTaskDescription;
      this.editingTaskId = null;
      this.editedTaskName = '';
      this.editedTaskDescription = '';
      this.saveTasksToLocalStorage();
    },
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode;
      document.body.style.backgroundColor = this.isDarkMode ? 'black' : '';
      document.body.style.color = this.isDarkMode ? 'white' : '';
      
    },
    confirmDeleteTask(id) {
      const isConfirmed = window.confirm("Are you sure you want to delete this task?");
      if (isConfirmed) {
        this.deleteTask(id);
        this.saveTasksToLocalStorage();
      }
    },
    saveTasksToLocalStorage() {
      // Save tasks to localStorage
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },
    fetchTasksFromLocalStorage() {
      // Fetch tasks from localStorage
      const savedTasks = localStorage.getItem('tasks');
      if (savedTasks) {
        this.tasks = JSON.parse(savedTasks);
      }

      // Fetch dark mode state from localStorage
      const darkMode = localStorage.getItem('darkMode');
      if (darkMode) {
        this.isDarkMode = JSON.parse(darkMode);
        document.body.style.backgroundColor = this.isDarkMode ? 'black' : '';
        document.body.style.color = this.isDarkMode ? 'white' : '';
      }
    },
    
  },
  mounted() {
    // Fetch tasks from localStorage when the component is mounted
    this.fetchTasksFromLocalStorage();
  },
  beforeUpdate() {
    // Save tasks to localStorage whenever there is a change
    this.saveTasksToLocalStorage();
  },
};
</script>



