<template>
  <div class="component-item" :style="itemStyle" @mousedown="handleMouseDown">
    <div v-if="item.type === 'text'" class="text">
      <input
        v-if="editable"
        v-model="inputValue"
        @blur="handleBlur"
        @keydown.enter.prevent="handleBlur"
      />
      <template v-else>{{ item.value }}</template>
    </div>
    <div v-else-if="item.type === 'square'" class="square">
      <div class="resize-handle" @mousedown.stop="handleResizeMouseDown"></div>
    </div>
    <div v-else-if="item.type === 'barcode'" class="barcode">[一维码]</div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watchEffect, computed } from "vue";

const props = defineProps({
  item: Object,
  index: Number,
});

const emit = defineEmits(["update-item"]);

const itemStyle = computed(() => ({
  left: `${props.item.x}px`,
  top: `${props.item.y}px`,
  width: props.item.width ? `${props.item.width}px` : "auto",
  height: props.item.height ? `${props.item.height}px` : "auto",
  fontSize: props.item.fontSize ? `${props.item.fontSize}px` : "inherit",
  position: "absolute",
}));

let mousemoveHandler = null;
let mouseupHandler = null;

const editable = ref(false);
const inputValue = ref(props.item.value);

const handleBlur = () => {
  editable.value = false;
  emit("update-item", props.index, {
    ...props.item,
    value: inputValue.value,
  });
};

const handleMouseDown = (event) => {
  const startX = event.clientX;
  const startY = event.clientY;
  const initialX = props.item.x;
  const initialY = props.item.y;

  mousemoveHandler = (e) => {
    const dx = e.clientX - startX;
    const dy = e.clientY - startY;
    emit("update-item", props.index, {
      ...props.item,
      x: initialX + dx,
      y: initialY + dy,
    });
  };

  mouseupHandler = () => {
    document.removeEventListener("mousemove", mousemoveHandler);
    document.removeEventListener("mouseup", mouseupHandler);
  };

  document.addEventListener("mousemove", mousemoveHandler);
  document.addEventListener("mouseup", mouseupHandler);

  if (props.item.type === "text") {
    editable.value = true;
  }
};

const handleResizeMouseDown = (event) => {
  const startX = event.clientX;
  const startY = event.clientY;
  const startWidth = props.item.width || 100;
  const startHeight = props.item.height || 100;

  mousemoveHandler = (e) => {
    const dx = e.clientX - startX;
    const dy = e.clientY - startY;
    emit("update-item", props.index, {
      ...props.item,
      width: startWidth + dx,
      height: startHeight + dy,
    });
  };

  mouseupHandler = () => {
    document.removeEventListener("mousemove", mousemoveHandler);
    document.removeEventListener("mouseup", mouseupHandler);
  };

  document.addEventListener("mousemove", mousemoveHandler);
  document.addEventListener("mouseup", mouseupHandler);
};

onUnmounted(() => {
  if (mousemoveHandler) {
    document.removeEventListener("mousemove", mousemoveHandler);
    document.removeEventListener("mouseup", mouseupHandler);
  }
});
</script>

<style scoped>
.component-item {
  user-select: none;
  cursor: move;
}

.text {
  background-color: white;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 4px;
  z-index: 2;
}

.square {
  border: 1px solid #ededed;
  /* background-color: rgba(255, 255, 255, 0.3); */
  background-color: #2c3e50;
  width: 100%;
  height: 100%;
  position: relative;
}
.resize-handle {
  /* position: absolute;
  bottom: 0;
  right: 0;
  width: 20px;
  height: 20px;
  background-color: brown;
  cursor: se-resize; */
  position: absolute;
  bottom: -4px;
  right: -4px;
  width: 8px;
  height: 8px;
  background-color: #3498db;
  border-radius: 50%;
  cursor: se-resize;
}

.barcode {
  background-color: white;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  z-index: 2;
}
</style>
