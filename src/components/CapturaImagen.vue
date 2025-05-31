<template>
  <div class="camara-container">
    <h3 class="camara-title">Cámara en Vivo 🎥</h3>

    <div class="video-wrapper">
      <video ref="video" autoplay playsinline muted></video>
    </div>

    <button class="capturar-btn" @click="capturarFoto">
      📸 Capturar Imagen
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, defineEmits } from "vue";

const video = ref(null);
const emit = defineEmits(["captura"]);
let stream = null;

onMounted(async () => {
  try {
    stream = await navigator.mediaDevices.getUserMedia({
      video: { facingMode: "environment" },
    });
    video.value.srcObject = stream;

    // Espera a que las dimensiones estén listas
    await new Promise((resolve) => {
      video.value.onloadedmetadata = () => {
        video.value.play();
        resolve();
      };
    });
  } catch (err) {
    alert("No se pudo acceder a la cámara");
    console.error(err);
  }
});

onBeforeUnmount(() => {
  if (stream) stream.getTracks().forEach((track) => track.stop());
});

function capturarFoto() {
  const videoEl = video.value;
  console.log('Se capturó foto desde la cámara');
  // Espera hasta que el video tenga dimensiones válidas
  if (videoEl.videoWidth === 0 || videoEl.videoHeight === 0) {
    alert("📷 Esperando que la cámara esté lista...");
    return;
  }

  const canvas = document.createElement("canvas");
  canvas.width = videoEl.videoWidth;
  canvas.height = videoEl.videoHeight;
  const ctx = canvas.getContext("2d");
  ctx.drawImage(videoEl, 0, 0);
  const dataUrl = canvas.toDataURL("image/jpeg");
  emit("captura", dataUrl);
}
</script>

<style scoped>
.camara-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
}

.camara-title {
  font-size: 1.3rem;
  color: #1b5e20;
  margin-bottom: 10px;
  font-weight: 600;
}

.video-wrapper {
  width: 100%;
  max-width: 480px; /* Antes era 100%, ahora lo limitamos */
  margin: 0 auto;
  border: 2px solid #c8e6c9;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.video-wrapper video {
  width: 100%;
  height: auto;
  display: block;
}

.capturar-btn {
  padding: 12px 30px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 30px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.capturar-btn:hover {
  background-color: #388e3c;
}
</style>
