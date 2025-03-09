<template>
    <div class="task-column">
      <h2>{{ title }}</h2>
      <draggable
        v-model="localTasks"
        :group="{ name: 'tasks', pull: true, put: true }"
        @end="onDragEnd"
      >
        <div v-for="task in localTasks" :key="task.id" class="task">
          <p>{{ task.text }}</p>
        </div>
      </draggable>
      <div v-if="isAddingTask" class="new-task-form">
        <input v-model="newTaskText" placeholder="Введите задачу" />
        <button @click="submitTask">Сохранить</button>
        <button @click="cancelAdding">Отмена</button>
      </div>
      <button v-else @click="isAddingTask = true">Добавить</button>
    </div>
  </template>
  
  <script>
  import draggable from 'vuedraggable';
  
  export default {
    components: {
      draggable,
    },
    props: {
      title: {
        type: String,
        required: true,
      },
      tasks: {
        type: Array,
        required: true,
      },
    },
    data() {
      return {
        localTasks: [...this.tasks], // Локальная копия массива задач
        isAddingTask: false,
        newTaskText: '',
      };
    },
    watch: {
      tasks(newTasks) {
        this.localTasks = [...newTasks];
      },
    },
    methods: {
      onDragEnd() {
        this.$emit('update-tasks', this.localTasks);
      },
      submitTask() {
        if (!this.newTaskText.trim()) return;
  
        const newTask = {
          id: Date.now(),
          text: this.newTaskText,
        };
  
        this.localTasks.push(newTask);
        this.newTaskText = '';
        this.isAddingTask = false;
        this.$emit('update-tasks', this.localTasks);
      },
      cancelAdding() {
        this.isAddingTask = false;
        this.newTaskText = '';
      },
    },
  };
  </script>
  
  <style scoped>
  .task-column {
    background: white;
    padding: 15px;
    width: 300px;
    min-height: 400px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  .new-task-form {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }
  </style>
  