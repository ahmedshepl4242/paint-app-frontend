<template>
  <Toolsbar
    ref="toolbar"
    @draw="(shapeType) => addShape(shapeType)"
    @startOperation="
      (type, data) => {
        startOperatoin(type, data);
      }
    "
    @endOperation="endOperation"
    @undo="startUndo"
    @redo="startRedo"
    @openModal="openLoadModal"
    @save="openSaveModal"
    @saveImage="startSave"
    @clear="startClear"
  />
  <div class="draw">
    <Draw
      ref="draw"
      :color="drawColor"
      :state="drawState"
      :isOperating="drawIsOperating"
      @endOperation="endOperation"
    />
  </div>
  <LoadModal
    ref="loadmodal"
    @fileSent="(fileContents, extension) => openFile(fileContents, extension)"
  />
  <SaveModal
    ref="savemodal"
    @saveFile="(name, extension) => saveAs(name, extension)"
  />
</template>

<script>
import Toolsbar from "./components/Toolsbar.vue";
import Draw from "./components/Draw.vue";
import LoadModal from "./components/LoadModal.vue";
import SaveModal from "./components/SaveModal.vue";

export default {
  components: { Toolsbar, Draw, LoadModal, SaveModal },

  data() {
    return {
      drawColor: null,
      drawState: null,
      drawIsOperating: false,
    };
  },
  created() {
    window.addEventListener("beforeunload", this.refresh);
  },

  methods: {
    refresh() {
      this.$refs.draw.refresh();
    },

    addShape(shapeType) {
      this.$refs.draw.addNewShape(shapeType);
    },
    startOperatoin(type, data) {
      this.drawIsOperating = true;
      if (type == "color") {
        this.startColor(data);
      } else if (type == "copy") {
        this.startCopy();
      } else if (type == "delete") {
        this.startDelete();
      } else if (type == "undo") {
        this.startUndo();
      } else if (type == "redo") {
        this.startRedo();
      }
    },
    startColor(color) {
      this.drawColor = color;
      this.drawState = "color";
    },
    startCopy() {
      this.drawState = "copy";
    },
    startDelete() {
      this.drawState = "delete";
    },
    startUndo() {
      this.$refs.draw.undo();
    },
    startRedo() {
      this.$refs.draw.redo();
    },
    startClear() {
      this.$refs.draw.refresh();
    },
    startSave() {
      this.$refs.draw.saveImage();
    },
    endOperation() {
      this.drawState = null;
      this.drawIsOperating = false;
      this.$refs.toolbar.clearOperation();
    },
    openLoadModal() {
      this.$refs.loadmodal.openModal();
    },
    openFile(fileContents, extension) {
      if (extension == ".json") {
        this.$refs.draw.loadJSN(fileContents);
      } else {
        this.$refs.draw.loadXML(fileContents);
      }
    },
    openSaveModal() {
      this.$refs.savemodal.openModal();
    },
    saveAs(name, extension) {
      this.$refs.draw.save(name, extension);
    },
  },
};
</script>

<style>
.draw {
  margin-top: 50px;
  /* width: 90vw; */
  width: 80%;

  height: 90vh;
  position: relative;
  left: 60px;
  /* margin-left: 2vw; */
  margin-left: 200px;
  margin-right: 8vw;
  border: 1px solid black;
  border-radius: 5px;
  background-color: white;
  box-shadow: 5px 10px 10px 5px grey;
  overflow: scroll;
}
</style>
