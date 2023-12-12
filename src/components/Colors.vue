<template>
  <li
    v-for="color in palette"
    :key="color"
    :style="{ backgroundColor: color }"
    :class="{ selected: selected == color , color}"
    @click="selectColor(color)"
  ></li>
  <pickColor class="pick" @pickcolor="selectColor" />
</template>

<script>
import pickColor from "./pickColor.vue";
export default {
  components: { pickColor },
  emits: ["colorEvent", "endColoring"],
  components: {
    pickColor,
  },

  data() {
    return {
      palette: [
        "red",
        "blue",
        "green",
        "yellow",
        "brown",
        "orange",
        "hotpink",
        "purple",
        "black",
        "white",
        "lightblue",
      ],
      selected: null,
    };
  },
  methods: {
    selectColor(color) {
      if (this.selected == color) {
        this.$emit("endColoring");
        this.clearSelected();
      } else {
        this.selected = color;
        console.log(this.color);
        this.$emit("colorEvent", color);
      }
    },

    clearSelected() {
      this.selected = null;
    },
  },
};
</script>

<style>
.pick {
  position: relative;
  top: 0;
  left: 90px;
  z-index: 1;
}
.row li {
  width: 20px;
  height: 20px;
  border: 5px;
  border-radius: 200%;
  margin-left: 5px;
  margin-bottom: 5px;
  border: 1px solid black;
}

.row .color:hover {
  border-width: 4px;
}

.row .selected {
  border-width: 4px;
}
</style>
