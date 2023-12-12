<template>
    <div class="modal-container" v-if="showModal">
        <div class="modal-content">
            <h2 class="header">Save file</h2><div>
                <input type="text" placeholder="Enter file name" v-model="fileName">
            </div>
            <button @click="saveAs('json')">Save as JSON</button>
            <button @click="saveAs('xml')">Save as XML</button>
            <div>
                <button @click="closeModal">Cancle</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
  data() {
    return {
      showModal: false,
      fileName: null
    };
  },
  methods: {
    openModal() {
      this.showModal = true;
    },
    closeModal() {
      this.fileName = null
      this.showModal = false;
    },
    saveAs(extension) {
        this.$emit('saveFile', this.fileName, extension)
        this.closeModal()
    }
  }
};
</script>

<style scoped>
  .modal-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  .modal-content {
    background: white;
    padding: 20px;
    border-radius: 8px;
    text-align: center;
  }
  .modal-content button {
    margin: 10px

  }
  
  .header{
    margin-bottom: 30px;
  }
</style>