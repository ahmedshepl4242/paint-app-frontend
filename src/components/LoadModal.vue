<template>
    <div class="modal-container" v-if="showModal">
        <div class="modal-content">
            <h2 class="header">Load file</h2>
            <input type="file" id="file" :accept="extension" placeholder="Enter path of the file" />
            <button @click="loadFile">Load</button>
            <button @click="closeModal">Cancle</button>
            <div class="radio">
                <input type="radio" id="json" name="filetype" value=".json" v-model="extension">
                <label for="json">JSON file</label><br>
                <input type="radio" id="xml" name="filetype" value=".xml" v-model="extension">
                <label for="xml">XML file</label><br>
            </div>
            <p>{{ message }}</p>
        </div>
    </div>
</template>
  
<script>
  export default {
    data() {
      return {
        showModal: false,
        message: null,
        extension: '.json'
      };
    },
    methods: {
      openModal() {
        this.showModal = true
      },
      closeModal() {
        this.message = ''
        this.showModal = false
      },
      loadFile() {
        let fileInput = document.getElementById('file');
        let file = fileInput.files[0];

        if (file && '.'+file.name.split('.').pop()==this.extension) {
            this.parseFile().then( fileContents=>{
                this.$emit('fileSent', fileContents, this.extension)
            })
            this.closeModal()
        }
        else {
            this.message = 'The file is not of type '+this.extension+'.'
        }
      },
      parseFile() {
        let fileInput = document.getElementById('file')
        let file = fileInput.files[0]
        let reader = new FileReader()

        return new Promise((resolve, reject) => {
        reader.onload = function(e) {
            resolve(e.target.result)
        }
        reader.onerror = function(e) {
            reject(e)
        }
        reader.readAsText(file)
        })
      }
    }
  }
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
  
  .header{
    margin-bottom: 30px;
  }

  .radio{
    margin: 30px;
  }
</style>
  