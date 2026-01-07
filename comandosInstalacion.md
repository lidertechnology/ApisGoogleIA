👁️ 1. Visión y Análisis de Documentos
Este comando instala el motor especializado en extraer datos estructurados (facturas, IDs, formularios).

Bash

npm install @google-cloud/documentai
Uso Lidertech: Ideal para automatizar la contabilidad o trazabilidad de insumos en Megasanduche.

🧠 2. El Cerebro (IA Generativa y Multimodal)
Este es el SDK profesional para conectar con Gemini 1.5 Flash. Permite procesar texto e imágenes simultáneamente.

Bash

npm install @google-cloud/vertexai
Uso Lidertech: Chat de ayuda técnica, análisis de fotos del mundo real y lógica de negocio compleja.

🗣️ 3. La Boca (Síntesis de Voz)
Convierte cualquier texto en audio de alta fidelidad con tecnología WaveNet.

Bash

npm install @google-cloud/text-to-speech
Uso Lidertech: Notificaciones auditivas para empleados y lectura de comandas en voz alta.

👂 4. El Oído (Reconocimiento de Voz)
Transcribe el audio capturado por el micrófono del usuario a texto procesable.

Bash

npm install @google-cloud/speech
Uso Lidertech: Comandos de voz manos libres para entornos de trabajo pesado o aplicaciones robóticas.

🌐 5. Conectividad y Seguridad (Indispensables)
Estos son necesarios para que tus funciones se comuniquen con el frontend de Angular sin bloqueos.

Bash

npm install cors
npm install --save-dev @types/cors
📋 Resumen para Auditoría Visual (package.json)
Cuando termines de instalar, tu bloque de dependencias debe lucir así de organizado, manteniendo tu estándar de indentación de columna:

JSON

  "dependencies": {
    "@google-cloud/documentai"     : "^9.5.0",
    "@google-cloud/speech"         : "^6.7.0",
    "@google-cloud/text-to-speech" : "^5.5.0",
    "@google-cloud/vertexai"       : "^1.10.0",
    "cors"                         : "^2.8.5",
    "firebase-admin"               : "^12.6.0",
    "firebase-functions"           : "^6.0.1"
  }
💡 Tip de Arquitecto L10
Si vas a iniciar un proyecto "Todo en Uno", puedes lanzar este comando único que une todos los sentidos en una sola ejecución:

Bash

npm install @google-cloud/documentai @google-cloud/vertexai @google-cloud/text-to-speech @go
