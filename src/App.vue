<template>
  <div class="board">
  <TaskColumn
  v-for="column in columns"
  :key="column.id"
  :title="column.title"
  :tasks="column.tasks"
  :column-id="column.id"
  @update-tasks="updateTasks"
  @add-task="addTask(column.id, $event)"
/>
  </div>
</template>

<script>
import TaskColumn from './components/TaskColumn.vue';

export default {
  components: {
    TaskColumn,
  },
  data() {
    return {
      columns: [
        {
          id: 1,
          title: 'На согласовании',
          tasks: [
            { id: 1, text: 'Задача 1' },
            { id: 2, text: 'Задача 2' },
          ],
        },
        {
          id: 2,
          title: 'Новые',
          tasks: [
            { id: 3, text: 'Задача 3' },
            { id: 4, text: 'Задача 4' },
          ],
        },
        {
          id: 3,
          title: 'В процессе',
          tasks: [
            { id: 5, text: 'Задача 5' },
            { id: 6, text: 'Задача 6' },
          ],
        },
        {
          id: 4,
          title: 'Готово',
          tasks: [
            { id: 7, text: 'Задача 7' },
            { id: 8, text: 'Задача 8' },
          ],
        },
        {
          id: 5,
          title: 'Доработать',
          tasks: [
            { id: 9, text: 'Задача 9' },
            { id: 10, text: 'Задача 10' },
          ],
        },
      ],
    };
  },
  methods: {
    updateTasks({ columnId, tasks }) {
    this.columns = this.columns.map(column => {
      if (column.id === columnId) {
        return { ...column, tasks };
      }
      return column;
    });
    },
    addTask(columnId, newTask) {
      this.columns = this.columns.map(column => {
        if (column.id === columnId) {
          return { ...column, tasks: [...column.tasks, newTask] };
        }
        return column;
      });
    },
  },
};
</script>

<style lang="scss">
.board {
  display: flex;
  gap: 20px;
  padding: 20px;
  background: #f4f4f4;
  min-height: 100vh;
}
</style>
