<template>
  <div class="subida-contenedor">
    <h3 class="subida-titulo">Subir Imagen desde Galería 📁</h3>

    <label class="subida-label">
      Seleccionar Imagen
      <input type="file" accept="image/*" @change="procesar" />
    </label>
  </div>
</template>

<script setup>
import { defineEmits } from 'vue'

const emit = defineEmits(['captura'])

function procesar(event) {
  const file = event.target.files[0]
  const reader = new FileReader()
  reader.onload = () => emit('captura', reader.result)
  if (file) reader.readAsDataURL(file)
}
</script>

<style scoped>
.subida-contenedor {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.subida-titulo {
  font-size: 1.2rem;
  color: #1b5e20;
  text-align: center;
  margin: 0;
}

.subida-label {
  background-color: #a5d6a7;
  color: #1b5e20;
  font-weight: bold;
  padding: 12px 24px;
  border-radius: 30px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  font-size: 1rem;
  text-align: center;
}

.subida-label:hover {
  background-color: #81c784;
}

.subida-label input[type="file"] {
  display: none;
}
</style>
