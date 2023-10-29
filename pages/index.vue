<template>
  <div>
    <p>{{ $route.query }}</p>
    <p class="decode-result">
      Last result: <b>{{ result }}</b>
    </p>

    <!-- <qrcode-stream :constraints="{ facingMode }" :paused="paused" @detect="onDetect" @error="onError"> </qrcode-stream> -->
    <qrcode-stream @detect="onDetect" :paused="paused"></qrcode-stream>
    <button @click="switchCamera">
      <img src="../assets/change-camera.png" alt="switch camera" style="width: 10%" />
    </button>
  </div>
</template>

<script setup>
import { QrcodeStream, QrcodeDropZone, QrcodeCapture } from "vue-qrcode-reader";

const isValid = ref(undefined);
const paused = ref(false);
const result = ref(null);
const facingMode = ref("environment");
const noRearCamera = ref(false);
const noFrontCamera = ref(false);

const onError = (error) => {
  const triedFrontCamera = facingMode.value === "user";
  const triedRearCamera = facingMode.value === "environment";
  const cameraMissingError = error.name === "OverconstrainedError";
  if (triedRearCamera && cameraMissingError) {
    noRearCamera.value = true;
  }
  if (triedFrontCamera && cameraMissingError) {
    noFrontCamera.value = true;
  }
  console.error(error);
};

const onDetect = ([firstDetectedCode]) => {
  result.value = firstDetectedCode.rawValue;
  paused.value = true;

  // pretend it's taking really long
  setTimeout(() => {
    isValid.value = result.value.startsWith("http");
    // some more delay, so users have time to read the message
    setTimeout(() => (paused.value = false), 2000);
  }, 3000);
};

const switchCamera = () => {
  switch (facingMode.value) {
    case "environment":
      facingMode.value = "user";
      break;
    case "user":
      facingMode.value = "environment";
      break;
  }
};
</script>

<style scoped>
.validation-success,
.validation-failure,
.validation-pending {
  position: absolute;
  width: 100%;
  height: 100%;

  background-color: rgba(255, 255, 255, 0.8);
  padding: 10px;
  text-align: center;
  font-weight: bold;
  font-size: 1.4rem;
  color: black;

  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
}
.validation-success {
  color: green;
}
.validation-failure {
  color: red;
}
</style>
