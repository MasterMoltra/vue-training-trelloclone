<template>
  <div class="list-reset">
    <div
      class="task"
      draggable
      @dragstart="pickupTask($event, taskIndex, columnIndex)"
      @click="goToTask(task)"
      @dragover.prevent
      @dragenter.prevent
      @drop.stop="
        moveTaskOrColumn($event, column.tasks, columnIndex, taskIndex)
      "
    >
      <span class="w-full flex-no-shrink font-bold">
        {{ task.name }}
      </span>
      <p v-if="task.description" class="w-full flex-no-shrink mt-1 text-sm">
        {{ task.description }}
      </p>
    </div>
  </div>
</template>

<script>
import { mapState } from "vuex";

export default {
  name: "ColumnTask",
  props: {
    task: {
      type: Object,
      required: true
    },
    taskIndex: {
      type: Number,
      required: true
    },
    column: {
      type: Object,
      required: true
    },
    columnIndex: {
      type: Number,
      required: true
    }
  },
  computed: {
    ...mapState(["board"])
  },
  methods: {
    goToTask(task) {
      this.$router.push({ name: "task", params: { id: task.id } });
    },
    pickupTask(e, taskIndex, fromColumnIndex) {
      e.dataTransfer.effectAllowed = "move";
      e.dataTransfer.dropEffect = "move";

      e.dataTransfer.setData("from-task-index", taskIndex);
      e.dataTransfer.setData("from-column-index", fromColumnIndex);
      e.dataTransfer.setData("type", "task"); // <--- New code to identify task
    },
    moveTaskOrColumn(e, toTasks, toColumnIndex, toTaskIndex) {
      const type = e.dataTransfer.getData("type");
      if (type === "task") {
        this.moveTask(
          e,
          toTasks,
          toTaskIndex !== undefined ? toTaskIndex : toTasks.length
        );
      }
    },
    moveTask(e, toTasks, toTaskIndex) {
      // <--- Added toTaskIndex
      const fromColumnIndex = e.dataTransfer.getData("from-column-index");
      const fromTasks = this.board.columns[fromColumnIndex].tasks;
      const fromTaskIndex = e.dataTransfer.getData("from-task-index");
      this.$store.commit("MOVE_TASK", {
        fromTasks,
        fromTaskIndex, // <-- added index
        toTasks,
        toTaskIndex // <-- added index
      });
    }
  }
};
</script>
