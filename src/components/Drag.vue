<template>
  <div>
    <div v-if="!file">
      <div
        :class="['dropZone', dragging ? 'dropZone-over' : '']"
        @dragenter="dragging = true"
        @dragleave="dragging = false"
      >
        <div class="dropZone-info" @drag="onChange">
          <span class="fa fa-cloud-upload dropZone-title"></span>
          <span class="dropZone-title">Drop file or click to upload</span>
          <div class="dropZone-upload-limit-info">
            <div>extension support: txt</div>
            <div>maximum file size: 5 MB</div>
          </div>
        </div>
        <input type="file" @change="onChange" />
      </div>
    </div>
    <div v-else class="dropZone-uploaded">
      <div class="dropZone-uploaded-info">
        <span class="dropZone-title"> {{ file.name }} uploaded</span>
        <button type="button" class="btn removeFile" @click="removeFile">
          Remove
        </button>
      </div>
    </div>
    <div class="button-container">
      <Button
        @click="getFengshui"
        :disabled="file === ''"
        text="Check fengshui"
      />
    </div>
    <!-- <div class="uploadedFile-info">
      <div>fileName: {{ file.name }}</div>
      <div>fileZise(bytes): {{ file.size }}</div>
      <div>extension：{{ extension }}</div>
    </div> -->
  </div>
</template>

<script>
import { defineComponent } from "@vue/runtime-core";
import Button from "./Button.vue";

export default defineComponent({
  data() {
    return {
      file: "",
      dragging: false,
    };
  },
  components: {
    Button,
  },
  methods: {
    onChange(e) {
      const files = e.target.files || e.dataTransfer.files;
      if (!files.length) {
        this.dragging = false;
        return;
      }

      this.createFile(files[0]);
      const reader = new FileReader();

      reader.onload = (e) => {
        console.log(e.target.result);
        this.$emit("load", e.target.result);
      };
      reader.readAsText(this.file);
    },
    createFile(file) {
      if (!file.type.match("text.*")) {
        alert("please select txt file");
        this.dragging = false;
        return;
      }

      if (file.size > 5000000) {
        alert("please check file size no over 5 MB.");
        this.dragging = false;
        return;
      }

      this.file = file;
      this.dragging = false;
    },
    removeFile() {
      this.file = "";
      this.$emit("remove-file");
    },
    getFengshui() {
      console.log("click");
      this.$emit("get-fengshui-file");
    },
  },
  computed: {
    extension() {
      return this.file ? this.file.name.split(".").pop() : "";
    },
  },
});
</script>
<style>
.button-container {
  margin: 1rem 0;
}
.dropZone {
  width: 100%;
  height: 100px;
  position: relative;
  border: 2px dashed #eee;
}

.dropZone:hover {
  border: 2px solid black;
}

.dropZone:hover .dropZone-title {
  color: black;
}

.dropZone-info {
  color: white;
  position: absolute;
  top: 50%;
  width: 100%;
  transform: translate(0, -50%);
  text-align: center;
}

.dropZone-title {
  color: white;
}

.dropZone input {
  position: absolute;
  cursor: pointer;
  top: 0px;
  right: 0;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
}

.dropZone-upload-limit-info {
  display: flex;
  justify-content: flex-start;
  flex-direction: column;
  font-size: 0.8rem;
  margin-top: 5px;
}

.dropZone-over {
  background: #5c5c5c;
  opacity: 0.8;
}

.dropZone-uploaded {
  width: 100%;
  height: 100px;
  position: relative;
  border: 2px dashed #eee;
}

.dropZone-uploaded-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: white;
  position: absolute;
  top: 50%;
  width: 100%;
  transform: translate(0, -50%);
  text-align: center;
}

.removeFile {
  width: 120px !important;
  opacity: 0.7;
  background: red !important;
}
</style>
