<template>
  <div
    class="column"
    draggable
    @drop="moveTaskOrColumn($event, column.tasks, columnIndex)"
    @dragover.prevent
    @dragenter.prevent
    @dragstart.self="pickupColumn($event, columnIndex)"
  >
    <div class="flex items-center mb-6 font-bold">
      {{ column.name }}
    </div>
    <ColumnTask
      v-for="(task, taskIndex) of column.tasks"
      :key="taskIndex"
      :task="task"
      :taskIndex="taskIndex"
      :column="column"
      :columnIndex="columnIndex"
    >
    </ColumnTask>
    <slot name="newTask"> </slot>
  </div>
</template>

<script>
import { mapState } from "vuex";
import ColumnTask from "@/components/ColumnTask.vue";

export default {
  name: "BoardColumn",
  components: {
    ColumnTask
  },
  props: {
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
    ...mapState(["board"]),
    isTaskOpen() {
      return this.$route.name === "task";
    }
  },
  methods: {
    pickupColumn(e, columnIndex) {
      e.dataTransfer.effectAllowed = "move";
      e.dataTransfer.dropEffect = "move";

      e.dataTransfer.setData("from-column-index", columnIndex);
      e.dataTransfer.setData("type", "column");
    },
    moveTaskOrColumn(e, toTasks, toColumnIndex, toTaskIndex) {
      const type = e.dataTransfer.getData("type");
      if (type === "task") {
      } else {
        this.moveColumn(e, toColumnIndex);
      }
    },
    moveColumn(e, toColumnIndex) {
      const fromColumnIndex = e.dataTransfer.getData("from-column-index");
      this.$store.commit("MOVE_COLUMN", {
        fromColumnIndex,
        toColumnIndex
      });
    }
  }
};
</script>
