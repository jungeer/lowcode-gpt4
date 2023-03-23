<template>
  <div class="low-code-platform">
    <div class="left-panel">
      <p>组件列表:</p>
      <draggable-item
        v-for="item in components"
        :key="item.type"
        :component="item"
        @dragstart="handleDragStart($event, item)"
      />
    </div>

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

    <div class="json-display">
      <p>JSON 数据展示区:</p>
      <pre>{{ jsonValue }}</pre>
      <!-- <Vue3JsonEditor v-model="jsonValue" /> -->
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";
import { Vue3JsonEditor } from "vue3-json-editor";
import DraggableItem from "./DraggableItem.vue";
import ComponentItem from "./ComponentItem.vue";

const components = [
  { type: "text", label: "文本类型" },
  { type: "square", label: "矩形" },
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
.low-code-platform {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: flex-start;
  padding: 20px;
  gap: 20px;
  font-family: "Arial", sans-serif;
  background-color: #f5f5f5;
}

.left-panel,
.right-panel {
  border: 1px solid #ccc;
  padding: 20px;
  border-radius: 4px;
  background-color: #ffffff;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

.left-panel {
  min-width: 200px;
}

.right-panel {
  position: relative;
  width: 800px;
  height: 600px;
}

.button-container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px;
  margin-top: 20px;
}

button {
  cursor: pointer;
  font-size: 16px;
  padding: 10px 20px;
  border-radius: 4px;
  border: none;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
  transition: background-color 0.2s;
}

.reset-button {
  background-color: #f44336;
  color: #ffffff;
}

.reset-button:hover {
  background-color: #e5380e;
}

.save-button {
  background-color: #4caf50;
  color: #ffffff;
}

.save-button:hover {
  background-color: #43a047;
}

.json-display {
  border: 1px solid #ccc;
  padding: 20px;
  border-radius: 4px;
  min-width: 200px;
  max-height: 600px;
  overflow-y: auto;
  white-space: pre-wrap;
  background-color: #f5f5f5;
}

/* JSON 编辑器样式 */
.json-editor :deep(.vjs-container) {
  font-family: "Roboto", sans-serif;
  color: #333;
  background-color: #f0f0f0;
}

/* 菜单栏样式 */
.json-editor :deep(.vjs-btn) {
  background-color: #2a6aa1;
  color: #fff;
  border: 1px solid #1f4d75;
  border-radius: 4px;
}

.json-editor :deep(.vjs-btn:hover) {
  background-color: #1f4d75;
}

/* JSON 编辑器内容区域 */
.json-editor :deep(.vjs-content) {
  background-color: #f5f5f5;
  border: 1px solid #ccc;
  border-radius: 4px;
}

/* 括号、逗号、冒号样式 */
.json-editor :deep(.vjs-bracket),
.json-editor :deep(.vjs-comma),
.json-editor :deep(.vjs-colon) {
  color: #666;
}

/* 属性名称样式 */
.json-editor :deep(.vjs-property) {
  color: #a61d24;
}

/* 属性值 - 字符串 */
.json-editor :deep(.vjs-string) {
  color: #1a8c1a;
}

/* 属性值 - 数字 */
.json-editor :deep(.vjs-number) {
  color: #1c00cf;
}

/* 属性值 - 布尔值 */
.json-editor :deep(.vjs-boolean) {
  color: #aa3e00;
}

/* 属性值 - 空值 */
.json-editor :deep(.vjs-null) {
  color: #808080;
}
</style>
