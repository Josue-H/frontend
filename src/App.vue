<template>
  <div class="container">
    <h1 class="title">Analizador de Cultivos 🌾</h1>

    <div class="panel">
      <CapturaImagen @captura="recibirImagen" />
      <SubidaDesdeGaleria @captura="recibirImagen" />
    </div>

    <ProcesarImagen
      v-if="imagenSeleccionada"
      :imagenBase64="imagenSeleccionada"
    />
  </div>
</template>

<script setup>
import { ref } from "vue";
import CapturaImagen from "./components/CapturaImagen.vue";
import SubidaDesdeGaleria from "./components/SubidaGaleria.vue";
import ProcesarImagen from "./components/ProcesarImagen.vue";

const imagenSeleccionada = ref(null);

function recibirImagen(base64) {
  imagenSeleccionada.value = base64;
}
</script>

<style scoped>
html, body {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
  overflow-x: hidden;
  box-sizing: border-box;
}

body {
  background: linear-gradient(to bottom right, #f1f8e9, #ffffff);
  background-attachment: fixed;
  margin: 0;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

.container {
  max-width: 100%;
  width: 100%;
  margin: 0 auto;
  padding: 16px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.title {
  font-size: 2.4rem;
  font-weight: bold;
  color: #1b5e20;
  margin-bottom: 10px;
  text-align: center;
  word-break: break-word;
}

.panel {
  border: 2px dashed #4caf50;
  border-radius: 12px;
  padding: 20px;
  background-color: #f1f8e9;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.05);
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.panel h3 {
  margin: 0;
  font-size: 1.3rem;
  color: #1b5e20;
  display: flex;
  align-items: center;
  gap: 8px;
  justify-content: center;
  text-align: center;
}

video {
  width: 100%;
  max-height: 300px;
  border-radius: 12px;
  border: 2px solid #c8e6c9;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  object-fit: contain;
  display: block;
}

.start-btn {
  margin-top: 10px;
  padding: 12px 20px;
  font-size: 1rem;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 30px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  align-self: center;
  max-width: 300px;
  width: 100%;
}

.start-btn:hover {
  background-color: #388e3c;
}

input[type="file"] {
  width: 100%;
  font-size: 0.95rem;
  padding: 10px;
  border-radius: 6px;
  border: 1px solid #c8e6c9;
  background-color: #fff;
  box-sizing: border-box;
}

@media screen and (max-width: 600px) {
  .title {
    font-size: 1.8rem;
  }

  .panel {
    padding: 16px;
  }

  video {
    max-height: 220px;
  }

  .start-btn {
    font-size: 0.95rem;
  }
}
</style>
