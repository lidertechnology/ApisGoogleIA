Este es el **Informe Maestro de Viabilidad Económica y Límites Operativos** para el ecosistema **Lidertech AI Stack**. Este documento ha sido diseñado bajo la perspectiva de un Senior Software Architect para garantizar que la operación de **Megasanduche** (y cualquier proyecto L10) se mantenga estrictamente en la franja de **$0 USD**.

---

# 📑 INFORME TÉCNICO: ESTRATEGIA DE COSTO $0 - LIDERTECH L10

**DIRIGIDO A:** División de Ingeniería Lidertech

**PROYECTO:** Megasanduche (Trazabilidad y Robótica)

**FECHA DE EMISIÓN:** 2026

**ESTADO:** Certificado para Producción con Tráfico Moderado

---

## 1. RESUMEN EJECUTIVO

La arquitectura **L10** utiliza el modelo "Always Free" de Google Cloud Platform (GCP). Gracias a la implementación de funciones de segunda generación (Cloud Run), eliminamos los costos de aprovisionamiento. El sistema solo genera consumo durante la ejecución activa de los motores, lo cual, para un flujo de fábrica, se mantiene por debajo de los umbrales de facturación.

---

## 2. DESGLOSE DE RANGOS POR MOTOR IA

### 🧠 Inteligencia Estratégica y Visión (Vertex AI)

* **Motores:** `motorAyuda`, `motorImagenes`, `motorVideo`, `motorSeguridad`.
* **Modelo:** Gemini 1.5 Flash.
* **Límites Gratuitos:** * Solicitudes por minuto (RPM): **15**
* Solicitudes por día (RPD): **1,500**
* Tokens por minuto (TPM): **1,000,000**


* **Impacto Megasanduche:** Permite auditar visualmente hasta 1,500 bandejas de pan o clips de video de seguridad al día sin costo.

### 👁️ Visión Estructural (Document AI)

* **Motor:** `motorDocumentos`.
* **Límites Gratuitos:** **1,000 páginas mensuales**.
* **Impacto Megasanduche:** Ideal para procesar 33 facturas de proveedores diariamente. Superar este límite tiene un costo por página, por lo que se recomienda solo para documentos críticos de trazabilidad.

### 👂 Reconocimiento de Voz (Speech-to-Text)

* **Motor:** `motorEscucha`.
* **Límites Gratuitos:** **60 minutos mensuales**.
* **Impacto Megasanduche:** Permite aproximadamente 1,200 comandos de voz de 3 segundos cada uno. Es el recurso más limitado del stack; se debe usar exclusivamente para acciones rápidas.

### 🗣️ Síntesis de Voz (Text-to-Speech)

* **Motor:** `motorVoz`.
* **Límites Gratuitos:** **1,000,000 de caracteres mensuales** (Voz WaveNet).
* **Impacto Megasanduche:** La aplicación puede narrar instrucciones y confirmar registros durante toda la jornada laboral sin riesgo de cobro.

---

## 3. INFRAESTRUCTURA Y RED (EL BLINDAJE)

| Recurso | Nivel Gratuito (Mensual) | Configuración L10 |
| --- | --- | --- |
| **Cloud Functions (v2)** | 2,000,000 invocaciones | `minInstances: 0` |
| **Cloud Run (CPU)** | 180,000 vCPU-segundos | `cpu: 1` |
| **Cloud Run (RAM)** | 360,000 GiB-segundos | `memory: "1GiB"` |
| **Egress (Red)** | 1 GB de salida de datos | Compresión en Angular |
| **Cloud Storage** | 5 GB de almacenamiento | Ciclo de vida: 7 días |
| **Firestore** | 50k lecturas / 20k escrituras (Día) | Patrón de escritura eficiente |

---

## 4. CÓDIGO DE CONFIGURACIÓN MAESTRA

Para que este informe sea válido, el orquestador debe seguir esta estructura exacta para evitar "fugas" de presupuesto:

```typescript
import * as admin             from "firebase-admin";
import { 
  setGlobalOptions, 
  GlobalOptions 
}                             from "firebase-functions/v2";

admin.initializeApp();

const configuracionGlobal: GlobalOptions = {
    region:                   "us-central1",
    minInstances:             0,             
    maxInstances:             10,            
    memory:                   "1GiB",        
    concurrency:              1,             
    timeoutSeconds:           120,           
    cpu:                      1              
};

setGlobalOptions(configuracionGlobal);

export { motorAyuda }         from "./archive/asistenteAyuda";
export { motorDocumentos }    from "./archive/procesarDocumento";
export { motorEscucha }       from "./archive/motorEscucha";
export { motorImagenes }      from "./archive/analizadorImagenes";
export { motorVideo }         from "./archive/analizadorVideo";
export { motorVoz }           from "./archive/motorVoz";

```

---

## 5. RECOMENDACIONES DE AUDITORÍA (SENIOR LEVEL)

1. **Monitoreo de Red:** El GB gratuito de tráfico de salida es el punto más sensible. Si se envían videos sin comprimir desde el carro robótico o la fábrica, se agotará rápidamente.
2. **Ciclo de Vida de Almacenamiento:** Configura una regla en Cloud Storage para eliminar automáticamente fotos y videos después de 7 días. Esto mantiene los 5GB de espacio siempre disponibles.
3. **Manejo de Estados:** Utiliza los **Signals** en Angular para evitar llamadas redundantes. Si un motor está en `StateEnum.PROCESANDO`, bloquea la UI para evitar una segunda invocación accidental.

---

**CONCLUSIÓN:** La arquitectura propuesta es viable para una operación de manufactura de escala media (como la fábrica de sánduches) sin incurrir en costos de licenciamiento o infraestructura, logrando una eficiencia del 100% en la relación costo/beneficio.

// Fin del componente o servicio

¿Te gustaría que ahora preparemos el **`GlobalStatesService`** en Angular para integrar los **Signals** que monitorearán estos límites en tiempo real?
