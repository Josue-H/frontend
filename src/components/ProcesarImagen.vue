<template>
  <div class="panel" v-if="resultado">
    <div class="analysis-result">
      <div class="image-preview">
        <img :src="resultado.dataUrl" />
      </div>
      <div class="details">
        <h2>Resultado del Análisis 🧠</h2>
        <ul>
          <li>
            <span class="icon-item">📐</span>
            <strong>Resolución original:</strong>
            {{ resultado.originalWidth }} × {{ resultado.originalHeight }} px
          </li>
          <li>
            <span class="icon-item">📏</span>
            <strong>Redimensionada a:</strong>
            {{ resultado.resizedWidth }} × {{ resultado.resizedHeight }} px
          </li>

          <!-- Mostrar análisis si existe -->
          <li v-if="analisis">
            <span class="icon-item">🌱</span>
            <strong>Resultado:</strong>
            <ul>
              <li v-for="(valor, clave) in analisis" :key="clave">
                <strong>{{ clave.replace(/_/g, ' ') }}:</strong> {{ valor }}
              </li>
            </ul>
          </li>

          <!-- Mensaje de espera -->
          <li v-if="esperando">
            <span class="icon-item">⏳</span>
            <strong>Analizando imagen...</strong>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'

const props = defineProps({
  imagenBase64: String
})

const resultado = ref(null)
const analisis = ref(null)
const esperando = ref(false)

async function procesarImagen(base64) {
  if (!base64 || !base64.startsWith('data:image')) {
    analisis.value = '❌ Imagen no válida.';
    return;
  }

  esperando.value = true
  analisis.value = null
  resultado.value = null

  const img = new Image()
  img.onload = async () => {
    let w = img.width
    let h = img.height
    const max = 1024
    if (w > h && w > max) {
      h = (h * max) / w
      w = max
    } else if (h > max) {
      w = (w * max) / h
      h = max
    }

    const canvas = document.createElement('canvas')
    canvas.width = w
    canvas.height = h
    const ctx = canvas.getContext('2d')
    ctx.drawImage(img, 0, 0, w, h)

    const resizedDataUrl = canvas.toDataURL('image/jpeg', 0.9)

    resultado.value = {
      dataUrl: resizedDataUrl,
      originalWidth: img.width,
      originalHeight: img.height,
      resizedWidth: w,
      resizedHeight: h
    }

    await enviarImagen(resizedDataUrl)
  }

  // 🔄 Forzar recarga de imagen
  img.src = ''
  requestAnimationFrame(() => {
    img.src = base64
  })
}

watch(() => props.imagenBase64, (base64) => {
  procesarImagen(base64)
})

onMounted(() => {
  if (props.imagenBase64) {
    procesarImagen(props.imagenBase64)
  }
})

async function enviarImagen(base64) {
  try {
    const body = { image_base64: base64 }

    const resp = await fetch('https://api-nq15.onrender.com/api/analyze-image', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(body)
    })

    const data = await resp.json()
    analisis.value = data.analisis
  } catch (err) {
    console.error('Error al enviar imagen:', err)
    analisis.value = { error: '❌ Error al obtener el análisis.' }
  } finally {
    esperando.value = false
  }
}


</script>

<style scoped>
.analysis-result {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: #e8f5e9;
  border-radius: 12px;
  padding: 25px;
  margin-top: 30px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.image-preview {
  max-width: 100%;
  margin-bottom: 20px;
}

.image-preview img {
  width: auto;
  max-width: 100%;
  max-height: 320px;
  border-radius: 12px;
  border: 2px solid #c8e6c9;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
}

.details {
  text-align: left;
  width: 100%;
  max-width: 500px;
}

.details h2 {
  margin-top: 0;
  margin-bottom: 15px;
  color: #1b5e20;
  text-align: center;
}

.details ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.details ul li {
  display: flex;
  flex-direction: column;
  margin-bottom: 12px;
  font-size: 1rem;
  padding: 6px 0;
  border-bottom: 1px dashed #c8e6c9;
}

.details .icon-item {
  margin-bottom: 4px;
  font-size: 1.3em;
}
</style>
