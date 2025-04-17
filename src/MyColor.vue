<script setup>
import { ref, computed } from 'vue';

const colorValue = ref([{ color: 0, name: 'r' }, { color: 0, name: 'g' }, { color: 0, name: 'b' }]);
 
const rgbToHex = computed({
  get() {
   
    const rgb = `rgb(${colorValue.value[0].color},${colorValue.value[1].color},${colorValue.value[2].color})`;
    const result = rgb.match(/\d+/g);
    if (!result || result.length < 3) return '';
    return `#${result.map(x => parseInt(x).toString(16).padStart(2, '0')).join('')}`;
  },
  set(hex) {
    const cleanHex = hex.replace('#', '').trim();
    if (/^[0-9A-Fa-f]{6}$/.test(cleanHex)) {//检查满足正则
      const r = parseInt(cleanHex.slice(0, 2), 16);
      const g = parseInt(cleanHex.slice(2, 4), 16);
      const b = parseInt(cleanHex.slice(4, 6), 16);
      colorValue.value[0].color = r;
      colorValue.value[1].color = g;
      colorValue.value[2].color = b;
    }
  }
});

const textColor = computed(() => {
  if (colorValue.value[0].color <= 128 && colorValue.value[1].color <= 128 && colorValue.value[2].color <= 128) {
    return 'white';
  }
  return 'black';
});

function numberlimit(index) {
  if (colorValue.value[index].color > 255) {
    colorValue.value[index].color = 255;
  }
  if (colorValue.value[index].color < 0){
     colorValue.value[index].color = 0;
  }
}

//   处理 HEX 输入，限制只接受 0-9、A-F、a-f 字符，并确保长度不超过 6 位
function restrictHexInput(event) {
  const input = event.target;
  let value = input.value.replace('#', '').toUpperCase(); // 移除 #，转为大写
  value = value.replace(/[^0-9A-Fa-f]/g, ''); // 只保留 0-9、A-F、a-f
  if (value.length > 6) {
    value = value.slice(0, 6); // 限制最大 6 位
  }
  input.value = value ? `#${value}` : ''; // 添加 # 前缀
}
</script>

<template>
  <div class="container">
    <div class="display-block" :style="{ background: rgbToHex }">
      <input
        type="text"
        v-model="rgbToHex"
        :style="{ color: textColor, border: 'none', background: 'transparent', textAlign: 'center' }"
        placeholder= "0-9 A-F a-f"
        
        maxlength="7"  
        @input="restrictHexInput"
      />
    </div>

    <div class="bar-block">
      <div v-for="(item, index) in colorValue" :key="index">
        <label :for="item.name">{{ item.name }}:</label>
        <input type="range" :id="item.name" min="0" max="255" v-model="colorValue[index].color">
        <input type="number" class="number-input" min="0" max="255" v-model="colorValue[index].color"
          @input="numberlimit(index)">
      </div>
    </div>
    <slot />
  </div>
</template>

<style>
.container {
  width: fit-content;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  padding: 20px;
  border: 2px solid gray;
  margin: 0 0 40px;
  border-radius: 12px;
}

.display-block {
  width: 100%;
  height: 90px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.bar-block div {
  display: flex;
  gap: 4px;
  margin-top: 10px;
  justify-content: space-between;
}

.number-input {
  width: 40px;
}

 
.display-block input {
  width: 80px;
  font-size: 16px;
}

/* 优化输入框外观 */
.display-block input {
  outline: none;  
}
</style>