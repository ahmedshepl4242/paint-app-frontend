<template>
  <ul class="toolbar">
    <li @click="showBar = 'file'">file</li>
    <li @click="showBar = 'draw'">draw</li>
    <li @click="showBar = 'edit'">edit</li>
    <li @click="showBar = 'color'">color</li>
    <li @click="recieveUndo">undo</li>
    <li @click="recieveRedo">redo</li>
    <li @click="recieveClear">clear</li>
    <li @click="recieveSaveImage">Save As Image</li>
  </ul>

  <ul class="toolbar lowerbar" v-show="showBar == 'draw'">
    <li
      v-for="buttonName in drawButtons"
      :key="buttonName.id"
      @click="recieveDraw(buttonName.type)"
    >
      {{ buttonName.type }}
    </li>
  </ul>

  <ul class="toolbar lowerbar" v-show="showBar == 'file'">
    <li @click="recieveSave">save</li>
    <li @click="openModal">load</li>
  </ul>

  <ul class="toolbar lowerbar" v-show="showBar == 'edit'">
    <li @click="recieveCopy" :class="{ selected: activeOperation == 'copy' }">
      copy
    </li>
    <li
      @click="recieveDelete"
      :class="{ selected: activeOperation == 'delete' }"
    >
      delete
    </li>
  </ul>
  <ul class="toolbar lowerbar row" v-show="showBar == 'color'">
    <Colors
      ref="colors"
      @colorEvent="
        (color) => {
          recieveColor(color);
        }
      "
      @endColoring="EndOperation"
    />
  </ul>
</template>

<script>
import Colors from "./Colors.vue";
export default {
  emits: ["draw", "startOperation", "endOperation", "undo", "redo", 'openModal','save',
  'saveImage', 'clear'],

  components: {
    Colors,
  },
  data() {
    return {
      drawButtons: [
        { id: 1, type: "line" },
        { id: 2, type: "circle" },
        { id: 3, type: "ellipse" },
        { id: 4, type: "triangle" },
        { id: 5, type: "rectangle" },
        { id: 6, type: "square" },
      ],
      showBar: "draw",
      activeOperation: null,
    };
  },
  methods: {
    recieveDraw(shapeType) {
      this.$emit("draw", shapeType);
    },
    recieveColor(color) {
      this.$emit("startOperation", "color", color);
    },
    recieveCopy() {
      this.$emit("startOperation", "copy");
      this.toggleOperation("copy");
    },
    recieveDelete() {
      this.$emit("startOperation", "delete");
      this.toggleOperation("delete");
    },
    recieveUndo() {
      this.$emit("undo");
    },
    recieveRedo() {
      this.$emit("redo");
    },
    recieveClear() {
      this.$emit("clear");
    },
    recieveSaveImage() {
      this.$emit("saveImage");
    },

    toggleOperation(operation) {
      if (this.activeOperation == operation) {
        this.clearOperation();
        this.EndOperation();
      } else {
        this.activeOperation = operation;
      }
    },
    EndOperation() {
      this.$emit("endOperation");
    },
    clearOperation() {
      this.activeOperation = null;
      this.$refs.colors.clearSelected();
    },
    openModal() {
      this.$emit('openModal')
    },
    recieveSave() {
      this.$emit('save')
    }
  },
};
</script>

<style>
ul.toolbar {
  list-style-type: none;
  margin: 0;
  padding: 0;
  background-color: white;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  font-size: small;
  height: 30px;
}

ul.lowerbar {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 60px;
  height: 100%;
  background-color: #f0f0f0;
  padding: 5px;
  top: 30px;
  left: 0;
  font-size: unset;
}

ul.toolbar li {
  float: left;
  display: block;
  color: black;
  text-align: center;
  padding: 5px 16px;
  user-select: none;
}

ul.toolbar li:hover {
  background-color: grey;
}

.lowerbar .selected {
  background-color: grey;
}
</style>
