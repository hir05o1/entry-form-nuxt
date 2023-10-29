<template><qrcode-stream @detect="onDetect"></qrcode-stream></template>
<script setup>
import { QrcodeStream, QrcodeDropZone, QrcodeCapture } from "vue-qrcode-reader";
</script>
