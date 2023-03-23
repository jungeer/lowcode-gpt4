<template>
  <div class="low-code-platform">
    <div>
      <p class="title">组件列表:</p>
      <div class="left-panel">
        <draggable-item
          v-for="item in components"
          :key="item.type"
          :component="item"
          @dragstart="handleDragStart($event, item)"
        />
      </div>
    </div>

    <div>
      <p class="title">组件区:</p>
      <div
        class="right-panel"
        ref="visualArea"
        @dragover.prevent
        @dragenter.prevent
        @drop="handleDrop"
      >
        <component-item
          v-for="(item, index) in items"
          :key="index"
          :item="item"
          :index="index"
          @update-item="updateItem"
          @remove-item="removeItem"
        />
      </div>
      <div class="button-container">
        <button class="reset-button" @click="reset">重置</button>
        <button class="save-button" @click="save">保存</button>
      </div>
    </div>

    <div>
      <p class="title">JSON 数据展示区:</p>
      <div class="json-display">
        <pre>{{ jsonValue }}</pre>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";
import DraggableItem from "./DraggableItem.vue";
import ComponentItem from "./ComponentItem.vue";

const components = [
  { type: "text", label: "文本/输入框" },
  { type: "square", label: "矩形框" },
  { type: "barcode", label: "一维码" },
];
const items = ref([]);
const visualArea = ref(null);

const handleDragStart = (event, component) => {
  event.dataTransfer.setData("application/json", JSON.stringify(component));
};

const handleDrop = (event) => {
  event.preventDefault();
  const componentData = event.dataTransfer.getData("application/json");
  console.log("com ", componentData);
  if (!componentData || componentData === "undefined") return;

  console.log("drop");

  const component = JSON.parse(componentData);
  const { x, y } = visualArea.value.getBoundingClientRect();
  const item = {
    ...component,
    x: event.clientX - x,
    y: event.clientY - y,
  };

  items.value.push(item);
};

const jsonValue = ref("");

watch(
  items,
  () => {
    // jsonValue.value = items.value;
    jsonValue.value = JSON.stringify(items.value, null, 2);
  },
  { deep: true }
);

const updateItem = (index, newItem) => {
  items.value[index] = newItem;
};

const removeItem = (index) => {
  items.value.splice(index, 1);
};

const reset = () => {
  items.value = [];
};

const save = () => {
  const output = items.value.map((item) => {
    if (item.type === "square") {
      return {
        ...item,
        x2: item.x + (item.width || 100),
        y2: item.y + (item.height || 100),
      };
    }
    return item;
  });
  console.log(output);
};
</script>

<style scoped>
.title {
  font-size: 24px;
  font-weight: bold;
  color: #2c3e50;
  margin-bottom: 10px;
}

.low-code-platform {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: flex-start;
  padding: 20px;
  gap: 20px;
  font-family: "Roboto", "Arial", sans-serif;
  background-color: #ecf0f1;
}

.left-panel,
.right-panel {
  border-radius: 6px;
  background-color: #ffffff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.left-panel {
  display: flex;
  flex-direction: column;
  gap: 15px;
  min-width: 250px;
  padding: 20px;
  height: 500px;
}

.right-panel {
  position: relative;
  padding: 20px;
  width: 500px;
  height: 500px;
}

.button-container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 15px;
  margin-top: 20px;
}

button {
  cursor: pointer;
  font-size: 16px;
  font-weight: bold;
  padding: 10px 20px;
  border-radius: 30px;
  border: none;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
  transition: background-color 0.2s, color 0.2s, box-shadow 0.2s;
}

.reset-button {
  background-color: #e74c3c;
  color: #ffffff;
}

.reset-button:hover {
  background-color: #c0392b;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
}

.save-button {
  background-color: #2ecc71;
  color: #ffffff;
}

.save-button:hover {
  background-color: #27ae60;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
}

.json-display {
  border-radius: 6px;
  padding: 20px;
  min-width: 250px;
  height: 500px;
  max-height: 600px;
  overflow-y: auto;
  white-space: pre-wrap;
  background-color: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}
</style>
