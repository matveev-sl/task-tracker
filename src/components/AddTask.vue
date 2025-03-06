<template>
    <div class="add-task">
      <input 
        v-model="newTaskText" 
        placeholder="Введите задачу..." 
        type="text"
        :disabled="isSubmitting"
      />
      <button @click="submitTask" :disabled="isSubmitting">Добавить</button>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        newTaskText: '',
        isSubmitting: false,
      };
    },
    methods: {
      submitTask() {
        if (!this.newTaskText) return;
  
        this.isSubmitting = true;
        const newTask = {
          id: Date.now(),
          text: this.newTaskText,
        };
  
        this.$emit('add-task', newTask);
        
        this.newTaskText = ''; 
        this.isSubmitting = false; 
      }
    }
  };
  </script>
  
  <style scoped>
  .add-task {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }
  input {
    padding: 8px;
    font-size: 14px;
    border: 1px solid #ddd;
    border-radius: 4px;
  }
  button {
    padding: 8px 16px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
  </style>
  