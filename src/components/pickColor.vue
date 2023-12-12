<template>
  <div :style="{ background: color }" class="hu-color-picker">
    <button @click="sendColor">pick</button>

    <ColorPicker
      theme="light"
      :color="color"
      :sucker-hide="true"
      :sucker-canvas="suckerCanvas"
      :sucker-area="suckerArea"
      @changeColor="changeColor"
    />
  </div>
</template>

<script>
import { ColorPicker } from "vue-color-kit";
import "vue-color-kit/dist/vue-color-kit.css";

export default {
  components: {
    ColorPicker,
  },
  data() {
    return {
      color: "#59c7f9",
      suckerCanvas: null,
      suckerArea: [],
      isSucking: false,
    };
  },
  methods: {
    changeColor(color) {
      const { r, g, b, a } = color.rgba;
      this.color = `rgba(${r}, ${g}, ${b}, ${a})`;
    },
    sendColor() {
      console.log(this.color);
      this.$emit("pickcolor", this.color);
    },
  },
};
</script>
<style>
button {
  width: 100%;
  height: 40px;
  background-color: #112bb0;
  color: white;
  border: 1px solid #59c7f9;
  border-radius: 5px;
  cursor: pointer;
  font-size: 20px;
}
button:hover {
  width: 100%;

  height: 40px;

  background-color: #070707;
  color: white;
  border: 1px solid #59c7f9;
  border-radius: 5px;
  cursor: pointer;
}
.hu-color-picker {
  padding: 10px;
  background: #1d2024;
  border-radius: 4px;
  box-shadow: 0 0 16px 0 rgba(0, 0, 0, 0.16);
  z-index: 1;
  margin-bottom: 30px;
}
.hu-color-picker.light {
  background: #f7f8f9;
}
.hu-color-picker.light .color-show .sucker {
  background: #eceef0;
}
.hu-color-picker.light .color-type .name {
  background: #e7e8e9;
}
.hu-color-picker.light .color-type .value {
  color: #666;
  background: #eceef0;
}
.hu-color-picker.light .colors.history {
  border-top: 1px solid #eee;
}
.hu-color-picker canvas {
  vertical-align: top;
}
.hu-color-picker .color-set {
  display: flex;
}
.hu-color-picker .color-show {
  margin-top: 8px;
  display: flex;
}
.saturation {
  position: relative;
  cursor: pointer;
}
.saturation .slide {
  position: absolute;
  left: 100px;
  top: 0;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  border: 1px solid #fff;
  box-shadow: 0 0 1px 1px rgba(0, 0, 0, 0.3);
  pointer-events: none;
}
.color-type {
  display: flex;
  margin-top: 8px;
  font-size: 12px;
}
.color-type .name {
  width: 60px;
  height: 30px;
  float: left;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #999;
  background: #252930;
}
.color-type .value {
  flex: 1;
  height: 30px;
  min-width: 100px;
  padding: 0 12px;
  border: 0;
  color: #fff;
  background: #2e333a;
  box-sizing: border-box;
}
.color-alpha {
  position: relative;
  margin-left: 8px;
  cursor: pointer;
}
.color-alpha .slide {
  position: absolute;
  left: 0;
  top: 100px;
  width: 100%;
  height: 4px;
  background: #fff;
  box-shadow: 0 0 1px 0 rgba(0, 0, 0, 0.3);
  pointer-events: none;
}
.sucker {
  width: 30px;
  fill: #9099a4;
  background: #2e333a;
  cursor: pointer;
  transition: all 0.3s;
}
.sucker.active,
.sucker:hover {
  fill: #1593ff;
}
.colors {
  padding: 0;
  margin: 0;
}
.colors.history {
  margin-top: 10px;
  border-top: 1px solid #2e333a;
}
.colors .item {
  display: none;
}
.hue {
  position: relative;
  margin-left: 8px;
  cursor: pointer;
}
.hue .slide {
  position: absolute;
  left: 0;
  top: 100px;
  width: 100%;
  height: 4px;
  background: #fff;
  box-shadow: 0 0 1px 0 rgba(0, 0, 0, 0.3);
  pointer-events: none;
}
</style>
