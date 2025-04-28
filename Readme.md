# Steve Assistant API

API para "Steve", un asistente estratégico de alta gerencia basado en LLM con capacidades de síntesis de voz.

## Descripción

Esta API convierte el asistente de consola original en un servicio web que puede ser desplegado en Render. Proporciona endpoints para:

- Chatear con el asistente (respuestas en texto)
- Recibir respuestas en formato de audio (TTS)
- Gestionar sesiones de conversación
- Configurar preferencias del asistente

## Despliegue en Render

### Método 1: Despliegue directo desde GitHub

1. Crea un repositorio en GitHub con estos archivos
2. Inicia sesión en [Render](https://render.com/)
3. Haz clic en "New" y selecciona "Web Service"
4. Conecta con el repositorio de GitHub
5. Configura el servicio:
   - **Name**: steve-assistant (o el nombre que prefieras)
   - **Runtime**: Python 3
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `gunicorn app:app`
6. Configura las variables de entorno:
   - `OLLAMA_URL`: URL de la API de Ollama
   - `MODEL_NAME`: Nombre del modelo (por defecto: llama3:8b)
   - `TTS_VOICE`: Voz para TTS (por defecto: es-MX-JorgeNeural)
   - `TTS_RATE`: Velocidad de la voz (por defecto: +0%)
   - `TTS_VOLUME`: Volumen de la voz (por defecto: +0%)
7. Haz clic en "Create Web Service"

### Método 2: Despliegue con render.yaml

1. Utiliza el archivo `render.yaml` incluido
2. Desde el panel de Render, selecciona "New" -> "Blueprint"
3. Conecta con el repositorio que contiene el archivo render.yaml
4. Sigue las instrucciones para completar la configuración

## Endpoints de la API

### 1. GET `/`
Ruta de bienvenida que muestra los endpoints disponibles.

### 2. POST `/chat`
Envía un mensaje al asistente y recibe una respuesta en texto.

**Payload:**
```json
{
  "message": "¿Qué estrategia recomiendas para expandirnos a nuevos mercados?",
  "session_id": "usuario123"  // Opcional, se crea uno por defecto
}
```

**Respuesta:**
```json
{
  "response": "William, para la expansión de Antares Innovate a nuevos mercados, recomiendo...",
  "session_id": "usuario123"
}
```

### 3. POST `/speak`
Envía un mensaje al asistente y recibe un archivo de audio MP3 con la respuesta.

**Payload:** Similar a `/chat`

**Respuesta:** Archivo de audio MP3 (o respuesta de error en JSON si falla)

### 4. POST `/reset`
Reinicia una sesión de conversación.

**Payload:**
```json
{
  "session_id": "usuario123"  // Opcional
}
```

### 5. GET `/voices`
Lista las voces disponibles en español.

### 6. GET `/health`
Verifica el estado del servicio.

## Configuración del Entorno

Para modificar la configuración del asistente, utiliza variables de entorno:

- `OLLAMA_URL`: URL de la API de Ollama
- `MODEL_NAME`: Nombre del modelo a utilizar
- `TTS_VOICE`: Voz para síntesis de texto a voz
- `TTS_RATE`: Velocidad de la voz (ej: "+10%", "-20%")
- `TTS_VOLUME`: Volumen de la voz (ej: "+10%", "-20%")

## Uso con un Frontend

Para integrar con un frontend:

1. Configura CORS si es necesario (modificando app.py)
2. Consume los endpoints desde tu aplicación frontend
3. Para la voz, utiliza el endpoint `/speak` y reproduce el audio resultante

Ejemplo de uso con fetch en JavaScript:
```javascript
// Enviar mensaje y recibir texto
async function sendMessage(message) {
  const response = await fetch('https://tu-app-en-render.onrender.com/chat', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ message, session_id: 'mi-sesion' })
  });
  const data = await response.json();
  return data.response;
}

// Enviar mensaje y recibir audio
async function sendMessageWithAudio(message) {
  const response = await fetch('https://tu-app-en-render.onrender.com/speak', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ message, session_id: 'mi-sesion' })
  });
  
  const blob = await response.blob();
  const audio = new Audio();
  audio.src = URL.createObjectURL(blob);
  audio.play();
}
```
