<template>
  <div>
    <button @click="deleteRow">Delete</button>
  </div>
</template>

<script>
export default {
  name: 'ButtonCellRender',
  props: {
    params: Object, // AG Grid passes cell data via 'params'
  },
  methods: {
    deleteRow() {
      this.params.context.deleteTask(this.params.data.id); // Call the delete function from context
    },
  },
};
</script>

<style scoped>
button {
  padding: 6px 12px;
  background-color: #e74c3c;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #c0392b;
}
</style>
