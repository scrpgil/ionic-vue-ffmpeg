<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Tab 1</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <video id="output-video" controls></video><br />
      <input type="file" id="uploader" v-on:change="trim($event)">
      <p id="message"></p>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent } from '@ionic/vue';
import { createFFmpeg, fetchFile } from "@ffmpeg/ffmpeg";

export default defineComponent({
  name: 'Tab1Page',
  components: { IonHeader, IonToolbar, IonTitle, IonContent, IonPage },
  methods: {
    async trim(event: any) {
      const ffmpeg = createFFmpeg({
        log: true,
        corePath: '/assets/js/ffmpeg-core.js',
      });
      const files: any = event.target.files

      const message: any = document.getElementById('message');
      const { name } = files[0];
      message.innerHTML = 'Loading ffmpeg-core.js';
      await ffmpeg.load();
      message.innerHTML = 'Start trimming';
      ffmpeg.FS('writeFile', name, await fetchFile(files[0]));
      await ffmpeg.run('-i', name, '-ss', '0', '-to', '1', 'output.mp4');
      message.innerHTML = 'Complete trimming';
      const data = ffmpeg.FS('readFile', 'output.mp4');

      const video: any = document.getElementById('output-video');
      video.src = URL.createObjectURL(new Blob([data.buffer], { type: 'video/mp4' }));
    }
  },
  // setup() {
  //   // app state
  //   const ffmpeg = createFFmpeg({
  //     log: true,
  //   });
  //   const message = ref("Click Start to Transcode");
  //   let video = ref(null);
  //   const file =
  //     process.env.NODE_ENV === "production"
  //       ? "/vue-app/flame.avi"
  //       : "/flame.avi";
  //   // methods
  //   async function transcode() {
  //     message.value = "Loading ffmeg-core.js";
  //     await ffmpeg.load();
  //     message.value = "Start transcoding";
  //     ffmpeg.FS("writeFile", "test.avi", await fetchFile(file));
  //     await ffmpeg.run("-i", "test.avi", "test.mp4");
  //     message.value = "Complete transcoding";
  //     const data = ffmpeg.FS("readFile", "test.mp4");
  //     video.value = URL.createObjectURL(
  //       new Blob([data.buffer], { type: "video/mp4" })
  //     );
  //   }
  //   return {
  //     video,
  //     message,
  //     transcode,
  //   };
  // },
});
</script>
