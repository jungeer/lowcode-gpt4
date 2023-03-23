<template>
  <div class="draggable-item" draggable="true" @dragstart="handleDragStart">
    {{ component.label }}
  </div>
</template>

<script setup>
import { defineProps } from "vue";

const props = defineProps({
  component: Object,
});

const handleDragStart = (event) => {
  event.dataTransfer.setData("text/plain", JSON.stringify(props.component));
};
</script>

<style scoped>
.draggable-item {
  background-color: #eee;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 10px;
  cursor: grab;
  user-select: none;
  margin-bottom: 10px;
}
</style>
