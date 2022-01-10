<template>
  <div class="container home">
    <div class="row">
      <div class="col-3"></div>
      <div class="col-6">
        <img v-bind:src=" previewBase64 " class="img-fluid" alt="" />
      </div>
      <div class="col-3"></div>
    </div>
    <br />
    <div class="row">
      <div v-show="seen1" class="col-12">
        <p><input type="file" v-on:change="fileSelected" /></p>
      </div>
      <div v-show="seen2" class="col-12">
        <button v-on:click="fileUpload">顔抽出</button>
      </div>
      <div v-show="seen3" class="col-12"><p>少々おまちください</p></div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, render } from "vue";
import axios from "axios";
import saveAs from "file-saver";

export default defineComponent({
  name: "Home",
  components: {
  },
  data: function () {
    return {
      fileInfo: "",
      fileName: "no01",
      seen0: true,
      seen1: true,
      seen2: false,
      seen3: false,
      bstr: {},
      file: {},
      previewBase64: {},
    };
  },
  methods: {
    fileSelected(event: any) {
      this.fileInfo = event.target.files[0];
      this.file = event as File;
      this.seen0 = false;
      this.seen2 = true;
      const reader = new FileReader();
      reader.onload = (event:any) => {
                    this.previewBase64 = event.target.result;
                }
                reader.readAsDataURL(event.target.files[0]);
    },
    async fileUpload(): Promise<void> {
      const formData: any = new FormData();
      formData.append("file", this.fileInfo);
      console.log(formData);
      console.log(...formData.entries());
      this.seen0 = false;
      this.seen1 = false;
      this.seen2 = false;
      this.seen3 = true;
      axios.post("http://127.0.0.1:8000/upload", formData, {responseType: "blob"}).then((response) => {
        let mineType = response.headers["content-type"];
        const brob = new Blob([response.data], {type: mineType});
        saveAs(brob, "face-image.jpg");
        this.seen0 = false;
        this.seen1 = true;
        this.seen3 = false;
      });
    },
  },
});
</script>
