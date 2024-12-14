<template>
  <div class="container">
    <h1>Nuxt.js AG Grid To-Do List</h1>

    <form @submit.prevent="createTask" class="task-form">
      <input v-model="newTask" placeholder="Enter task" />
      <input v-model="newStatus" placeholder="Enter status" />
      <button type="submit">Task</button>
    </form>

    <ag-grid-vue
      ref="gridRef"
      style="width: 100%; height: 400px"
      class="ag-theme-quartz"
      :rowData="[...rowData]"
      :columnDefs="colDefs"
      rowSelection="single"
      @grid-ready="onGridReady"
      :frameworkComponents="frameworkComponents"
      :context="gridContext"
    ></ag-grid-vue>
  </div>
</template>

<script>
import { ref } from "vue";
import { AgGridVue } from "ag-grid-vue3";
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-quartz.css";
import "ag-grid-community/styles/ag-theme-alpine.css";
import ButtonCellRender from "./buttonCellRender";

export default {
  name: "DataTable",
  components: {
    AgGridVue,
    ButtonCellRender,
  },
  setup() {
    const newTask = ref("");
    const newStatus = ref("pending");
    const gridApi = ref(null);
    const gridRef = ref(null);
    function onGridReady(params) {
      gridApi.value = params.api;
    }
    const rowData = ref([
      { id: 1, task: "Learn Nuxt.js", status: "paused" },
      { id: 2, task: "Implement AG Grid", status: "completed" },
      { id: 3, task: "Build To-Do App", status: "in-progress" },
    ]);

    const colDefs = ref([
      { headerName: "ID", field: "id", width: 100 },
      { headerName: "Task", field: "task", flex: 1, editable: true },
      {
        headerName: "Status",
        field: "status",
        flex: 1,
        editable: true,
        cellEditor: "agSelectCellEditor",
        cellEditorParams: {
          values: ["pending", "in-progress", "completed", "paused"],
        },
      },
      {
        headerName: "Actions",
        resizable: true,
        cellRenderer: "ButtonCellRender",
        width: 150,
      },
    ]);

    const frameworkComponents = {
      ButtonCellRender,
    };
    watch(rowData, () => {
      localStorage.setItem("rowData", JSON.stringify(rowData.value));
    });

    function createTask() {
      const taskName = newTask.value.trim();
      const taskStatus = newStatus.value.trim() || "pending";

      if (!taskName) {
        alert("Task name cannot be empty!");
        return;
      }

      const taskExists = rowData.value.some(
        (item) => item.task.toLowerCase() === taskName.toLowerCase()
      );

      if (taskExists) {
        alert("This task already exists!");
        return;
      }

      rowData.value.push({
        id: rowData.value.length + 1,
        task: taskName,
        status: taskStatus,
      });

      if (gridApi.value) {
        gridApi.value.setRowData(rowData.value);
        gridApi.value.refreshCells();
      }
      newTask.value = "";
      newStatus.value = "";
    }

    function editTask(id) {
      const task = rowData.value.find((item) => item.id === id);
      if (task) {
        newTask.value = task.task;
        newStatus.value = task.status;
      }
    }

    const deleteTask = (id) => {
      console.log(id);

      rowData.value = rowData.value.filter((row) => row.id !== id);
    };

    const gridContext = {
      deleteTask,
    };
    return {
      rowData,
      colDefs,
      newTask,
      newStatus,
      createTask,
      deleteTask,
      editTask,
      gridRef,
      onGridReady,
      gridContext,
      frameworkComponents,
    };
  },
};
</script>

<style>
.container {
  padding: 20px;
  max-width: 1000px;
  margin: auto;
}

.task-form {
  margin-bottom: 20px;
  display: flex;
  gap: 10px;
}

.task-form input {
  flex: 1;
  padding: 8px;
}

.task-form button {
  padding: 8px 16px;
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
}

.task-form button:hover {
  background-color: #0056b3;
}

.action-btn {
  margin: 2px;
  padding: 4px 8px;
  cursor: pointer;
}

.edit-btn {
  background-color: #ffc107;
  color: #fff;
  border: none;
}

.delete-btn {
  background-color: #dc3545;
  color: #fff;
  border: none;
}
</style>
