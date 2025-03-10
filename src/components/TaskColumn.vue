<template>
    <div class="task-column">
      <div class="title" :style="{ backgroundColor: titleBackgroundColor }">
        <h2>{{ title }}</h2>
      </div>
      <draggable
        class="task-border"
        v-model="localTasks"
        :group="{ name: 'tasks', pull: true, put: true }"
        @end="onDragEnd"
      >
        <div
          v-for="task in localTasks"
          :key="task.id"
          class="task"
          ref="taskElements"
        >
          <div class="task-content">
            <p v-if="!task.isEditing">{{ task.text }}</p>
            <div v-else class="editing-controls">
              <textarea v-model="task.editedText" />
              <button @click="confirmEdit(task)" class="icon-button">
                <img src="@/assets/img/check.svg" alt="Принять" class="icon" />
              </button>
              <button @click="cancelEdit(task)" class="icon-button">
                <img src="@/assets/img/x.svg" alt="Отмена" class="icon" />
              </button>
            </div>
          </div>
          <div class="task-actions">
            <button @click="toggleMenu(task.id)">⋮</button>
            <div v-if="task.showMenu" class="task-menu">
              <button class="change" @click="editTask(task)">
                <img src="@/assets/img/edit.svg" alt="Редактировать" class="icon" />
                Редактировать
              </button>
              <button class="change" @click="confirmDelete(task)">
                <img src="@/assets/img/trash.svg" alt="Удалить" class="icon" />
                Удалить
              </button>
            </div>
          </div>
          <div v-if="task.isConfirmingDelete" class="delete-confirmation">
            <p>Вы уверены, что хотите удалить эту задачу?</p>
            <button @click="deleteTask(task)">Да</button>
            <button @click="cancelDelete(task)">Нет</button>
          </div>
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
      titleBackgroundColor: {
        type: String,
        required: true,
      },
    },
    data() {
      return {
        localTasks: this.tasks.map((task) => ({
          ...task,
          isEditing: false,
          isConfirmingDelete: false,
          showMenu: false,
          editedText: task.text,
        })),
        isAddingTask: false,
        newTaskText: '',
      };
    },
    watch: {
      tasks(newTasks) {
        this.localTasks = newTasks.map((task) => ({
          ...task,
          isEditing: false,
          isConfirmingDelete: false,
          showMenu: false,
          editedText: task.text,
        }));
      },
    },
    mounted() {
      document.addEventListener('click', this.handleClickOutside);
    },
    beforeDestroy() {
      document.removeEventListener('click', this.handleClickOutside);
    },
    methods: {
      onDragEnd() {
        this.$emit('update-tasks', this.localTasks);
      },
      toggleMenu(taskId) {
        this.localTasks.forEach((task) => {
          if (task.id === taskId) {
            task.showMenu = !task.showMenu;
          } else {
            task.showMenu = false;
          }
        });
      },
      editTask(task) {
        task.isEditing = true;
        task.showMenu = false;
      },
      confirmEdit(task) {
        task.text = task.editedText;
        task.isEditing = false;
      },
      cancelEdit(task) {
        task.editedText = task.text;
        task.isEditing = false;
      },
      confirmDelete(task) {
        task.isConfirmingDelete = true;
        task.showMenu = false;
      },
      cancelDelete(task) {
        task.isConfirmingDelete = false;
      },
      deleteTask(task) {
        this.localTasks = this.localTasks.filter((t) => t.id !== task.id);
        this.$emit('update-tasks', this.localTasks);
      },
      submitTask() {
        if (!this.newTaskText.trim()) return;
  
        const newTask = {
          id: Date.now(),
          text: this.newTaskText,
          isEditing: false,
          isConfirmingDelete: false,
          showMenu: false,
          editedText: this.newTaskText,
        };
  
        this.localTasks.push(newTask);
        this.newTaskText = '';
        this.isAddingTask = false;
        this.$emit('update-ttasks', this.localTasks);
      },
      cancelAdding() {
        this.isAddingTask = false;
        this.newTaskText = '';
      },
      handleClickOutside(event) {
        const taskElements = this.$refs.taskElements;
        let clickedInside = false;
  
        taskElements.forEach((taskElement) => {
          if (taskElement.contains(event.target)) {
            clickedInside = true;
          }
        });
  
        if (!clickedInside) {
          this.localTasks.forEach((task) => {
            task.showMenu = false;
          });
        }
      },
    },
  };
  </script>
  
  <style scoped>
  h2 {
    font-family: TT Interphases Pro Variable;
    font-weight: 584;
    font-size: 14px;
    line-height: 125%;
  }
.title {
height: 32px;
width: 177.60000610351562;
height: 32;
gap: 8px;
border-top-left-radius: 8px;
border-top-right-radius: 8px;
padding-top: 7px;
padding-right: 38px;
padding-bottom: 7px;
padding-left: 38px;
background-color: white;
margin-top: 0px;
}

.task-column {
background: #E3E5E8;
width: 300px;
height: 596px;
border-radius: 8px;
box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
overflow-y: auto;
}
  .new-task-form {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }
  .task-actions {
  position: relative;
}

.task-menu {
  position: absolute;
  top: 0;
  left: 20px; 
  width: 140px;
  background-color: white;
  border: 1px solid #ddd;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  z-index: 1; 
}

.task-menu button {
  display: flex;
  align-items: center;
  width: 100%;
  padding: 4px;
  border: none;
  background: none;
  text-align: left;
  cursor: pointer;
}

.task-menu button .icon {
  width: 16px;
  height: 16px;
  margin-right: 8px;
}

.task-border {
    padding: 10px;
}
.task-menu button {
  display: block;
  width: 100%;
  padding: 4px;
  border: none;
  background: none;
  text-align: left;
}

.delete-confirmation {
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding: 10px;
  background-color: #f8d7da;
  border: 1px solid #f5c6cb;
  border-radius: 4px;
}

.new-task-form {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-top: 20px;
}

.new-task-form input {
  padding: 8px;
  font-size: 14px;
}

.new-task-form button {
  padding: 8px;
  font-size: 14px;
  cursor: pointer;
}

.task-content p {
font-family: TT Interphases Pro Variable;
font-weight: 400;
font-size: 14px;
line-height: 18px;
letter-spacing: 0%;
}

.editing-controls button {
  padding: 8px;
  font-size: 14px;
  cursor: pointer;
}
.change {
font-family: TT Interphases Pro Variable;
font-weight: 400;
font-size: 14px;
line-height: 18px;
letter-spacing: 0%;
vertical-align: middle;


}

.task-actions button {
  background: none;
  border: none;
  cursor: pointer;
}
.task {
    display: flex;

    border-radius: 4px;
    border-width: 1px;
    border-color: #C4CAD4;

    padding-top: 8px;
    
    padding-bottom: 8px;
    padding-left: 8px;
    background-color: white;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 5px;
}
.icon {
  width: 16px;
  height: 16px;
  margin-right: 8px;
  vertical-align: middle;
}

.icon-button {
  display: block;
  background: none;
  border: none;
  cursor: pointer;
  padding: 4px;
  margin-left: auto; 
}

.editing-controls {
  display: flex;
  align-items: center;
}

</style>
  