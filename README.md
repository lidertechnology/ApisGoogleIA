Esta es la **Guía Maestra de Infraestructura Lidertech**. He consolidado el comando de instalación, la configuración de blindaje económico y el código fuente de los 8 motores que conforman el ecosistema de inteligencia artificial de tu arquitectura **L10**.

---

### 1. El Kit Maestro (Instalación Total)

Ejecuta este comando en la carpeta `functions` para activar todos los sentidos de la IA y el núcleo de Firebase:

```bash
cd functions && npm install firebase-admin firebase-functions @google-cloud/documentai @google-cloud/vertexai @google-cloud/text-to-speech @google-cloud/speech cors puppeteer-core axios && npm install --save-dev @types/cors

```

---

### 2. Configuración Robusta Costo $0 (`src/index.ts`)

Este orquestador aplica el blindaje global. Al usar `minInstances: 0`, los servidores se apagan cuando no hay uso, eliminando cobros fijos.

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

// --- ORQUESTACIÓN DE SERVICIOS LIDERTECH ---
export { motorAyuda }         from "./archive/asistenteAyuda";
export { motorDocumentos }    from "./archive/procesarDocumento";
export { motorEscucha }       from "./archive/motorEscucha";
export { motorImagenes }      from "./archive/analizadorImagenes";
export { motorVideo }         from "./archive/analizadorVideo";
export { motorVoz }           from "./archive/motorVoz";
export { motorSeguridad }     from "./archive/motorSeguridad";
export { motorScraper }       from "./archive/scrapeUrl";





// Fin del componente o servicio

```

---

### 3. Enumeración de Estados (`src/enums/StateEnum.ts`)

```typescript
export enum StateEnum {
    INICIAL                   = "inicial",
    PROCESANDO                = "procesando",
    EXITO                     = "exito",
    ERROR                     = "error"
}

```

---

### 4. Biblioteca de Motores (`src/archive/`)

#### A. Cerebro Estratégico (`motorAyuda.ts`)

```typescript
import { onRequest }          from "firebase-functions/v2/https";
import { VertexAI }           from "@google-cloud/vertexai";
import cors                   from "cors";
import { StateEnum }          from "../enums/StateEnum";

const corsHandler             = cors({ origin: true });
const vertexAI                = new VertexAI({ project: "lider-tech", location: "us-central1" });
const model                   = vertexAI.getGenerativeModel({ model: "gemini-1.5-flash-001" });

export const motorAyuda = onRequest(async (req, res) => {
    corsHandler(req, res, async () => {
        const { prompt, context } = req.body;
        const estado = { paso: StateEnum.INICIAL, exito: false, data: "" };

        try {
        estado.paso           = StateEnum.PROCESANDO;
        const result          = await model.generateContent(`${context}\n${prompt}`);
        estado.data           = result.response.candidates?.[0]?.content?.parts?.[0]?.text || "";
        estado.paso           = StateEnum.EXITO;
        estado.exito          = true;
        res.status(200).send(estado);
        } catch (e) {
        estado.paso           = StateEnum.ERROR;
        res.status(500).send(estado);
        }
    });
});





// Fin del componente o servicio

```

#### B. Visión de Documentos (`motorDocumentos.ts`)

```typescript
import { onRequest }          from "firebase-functions/v2/https";
import { DocumentProcessorServiceClient } from "@google-cloud/documentai";
import { StateEnum }          from "../enums/StateEnum";

const client                  = new DocumentProcessorServiceClient();

export const motorDocumentos = onRequest(async (req, res) => {
    const { file, processor } = req.body;
    const estado = { paso: StateEnum.INICIAL, exito: false, data: null };

    try {
    estado.paso               = StateEnum.PROCESANDO;
    const [result]            = await client.processDocument({
        name:                 processor,
        rawDocument:          { content: file, mimeType: "application/pdf" }
    });
    estado.data               = result.document;
    estado.exito              = true;
    res.status(200).send(estado);
    } catch (e) {
    estado.paso               = StateEnum.ERROR;
    res.status(500).send(estado);
    }
});





// Fin del componente o servicio

```

#### C. El Oído (`motorEscucha.ts`)

```typescript
import { onRequest }          from "firebase-functions/v2/https";
import speech                 from "@google-cloud/speech";
import cors                   from "cors";
import { StateEnum }          from "../enums/StateEnum";

const corsHandler             = cors({ origin: true });
const client                  = new speech.SpeechClient();

export const motorEscucha = onRequest(async (req, res) => {
    corsHandler(req, res, async () => {
        const { audio }       = req.body;
        const estado = { paso: StateEnum.INICIAL, exito: false, text: "" };

        try {
        estado.paso           = StateEnum.PROCESANDO;
        const [response]      = await client.recognize({
            config:           { encoding: "LINEAR16", languageCode: "es-CO" },
            audio:            { content: audio }
        });
        estado.text           = response.results?.map(r => r.alternatives?.[0].transcript).join("\n") || "";
        estado.exito          = true;
        res.status(200).send(estado);
        } catch (e) {
        estado.paso           = StateEnum.ERROR;
        res.status(500).send(estado);
        }
    });
});





// Fin del componente o servicio

```

#### D. Visión de Imágenes (`motorImagenes.ts`)

```typescript
import { onRequest }          from "firebase-functions/v2/https";
import { VertexAI }           from "@google-cloud/vertexai";
import { StateEnum }          from "../enums/StateEnum";

const vertexAI                = new VertexAI({ project: "lider-tech", location: "us-central1" });
const model                   = vertexAI.getGenerativeModel({ model: "gemini-1.5-flash-001" });

export const motorImagenes = onRequest(async (req, res) => {
    const { img, query }      = req.body;
    const estado = { paso: StateEnum.INICIAL, exito: false, analysis: "" };

    try {
    estado.paso               = StateEnum.PROCESANDO;
    const content             = {
        contents: [{ role: "user", parts: [{ inlineData: { data: img, mimeType: "image/jpeg" } }, { text: query }] }]
    };
    const result              = await model.generateContent(content);
    estado.analysis           = result.response.candidates?.[0]?.content?.parts?.[0]?.text || "";
    estado.exito              = true;
    res.status(200).send(estado);
    } catch (e) {
    estado.paso               = StateEnum.ERROR;
    res.status(500).send(estado);
    }
});





// Fin del componente o servicio

```

#### E. Visión Temporal (`motorVideo.ts`)

```typescript
import { onRequest }          from "firebase-functions/v2/https";
import { VertexAI }           from "@google-cloud/vertexai";
import { StateEnum }          from "../enums/StateEnum";

const vertexAI                = new VertexAI({ project: "lider-tech", location: "us-central1" });
const model                   = vertexAI.getGenerativeModel({ model: "gemini-1.5-flash-001" });

export const motorVideo = onRequest(async (req, res) => {
    const { videoUri, task }  = req.body;
    const estado = { paso: StateEnum.INICIAL, exito: false, report: "" };

    try {
    estado.paso               = StateEnum.PROCESANDO;
    const result              = await model.generateContent({
        contents: [{ role: "user", parts: [{ fileData: { fileUri: videoUri, mimeType: "video/mp4" } }, { text: task }] }]
    });
    estado.report             = result.response.candidates?.[0]?.content?.parts?.[0]?.text || "";
    estado.exito              = true;
    res.status(200).send(estado);
    } catch (e) {
    estado.paso               = StateEnum.ERROR;
    res.status(500).send(estado);
    }
});





// Fin del componente o servicio

```

#### F. La Boca (`motorVoz.ts`)

```typescript
import { onRequest }          from "firebase-functions/v2/https";
import textToSpeech           from "@google-cloud/text-to-speech";
import { StateEnum }          from "../enums/StateEnum";

const client                  = new textToSpeech.TextToSpeechClient();

export const motorVoz = onRequest(async (req, res) => {
    const { text }            = req.body;
    const estado = { paso: StateEnum.INICIAL, exito: false, audio: "" };

    try {
    estado.paso               = StateEnum.PROCESANDO;
    const [response]          = await client.synthesizeSpeech({
        input:                { text: text },
        voice:                { languageCode: "es-CO", name: "es-CO-Wavenet-D" },
        audioConfig:          { audioEncoding: "MP3" }
    });
    estado.audio              = (response.audioContent as Buffer).toString("base64");
    estado.exito              = true;
    res.status(200).send(estado);
    } catch (e) {
    estado.paso               = StateEnum.ERROR;
    res.status(500).send(estado);
    }
});





// Fin del componente o servicio

```

#### G. Inteligencia Robótica (`motorSeguridad.ts`)

```typescript
import { onRequest }          from "firebase-functions/v2/https";
import { VertexAI }           from "@google-cloud/vertexai";
import { StateEnum }          from "../enums/StateEnum";

const vertexAI                = new VertexAI({ project: "lider-tech", location: "us-central1" });
const model                   = vertexAI.getGenerativeModel({ model: "gemini-1.5-flash-001" });

export const motorSeguridad = onRequest(async (req, res) => {
    const { videoUri }        = req.body;
    const estado = { paso: StateEnum.INICIAL, action: "STOP", reason: "" };

    try {
    estado.paso               = StateEnum.PROCESANDO;
    const prompt              = "Analiza el video de seguridad vehicular. Responde JSON: {action: 'STOP' | 'PROCEED', reason: 'string'}";
    const result              = await model.generateContent({
        contents: [{ role: "user", parts: [{ fileData: { fileUri: videoUri, mimeType: "video/mp4" } }, { text: prompt }] }]
    });
    const decision            = JSON.parse(result.response.candidates?.[0]?.content?.parts?.[0]?.text || "{}");
    estado.action             = decision.action;
    estado.reason             = decision.reason;
    res.status(200).send(estado);
    } catch (e) {
    estado.paso               = StateEnum.ERROR;
    res.status(500).send(estado);
    }
});





// Fin del componente o servicio

```

#### H. Investigador Web (`scrapeUrl.ts`)

```typescript
import { onRequest }          from "firebase-functions/v2/https";
import axios                  from "axios";
import { StateEnum }          from "../enums/StateEnum";

export const motorScraper = onRequest(async (req, res) => {
    const { url }             = req.body;
    const estado = { paso: StateEnum.INICIAL, exito: false, html: "" };

    try {
    estado.paso               = StateEnum.PROCESANDO;
    const response            = await axios.get(url);
    estado.html               = response.data;
    estado.exito              = true;
    res.status(200).send(estado);
    } catch (e) {
    estado.paso               = StateEnum.ERROR;
    res.status(500).send(estado);
    }
});





// Fin del componente o servicio

```

---

### ¿Qué has construido hoy?

Has consolidado un **Ecosistema Autónomo**. Con este stack, tus apps pueden interactuar con el mundo físico de forma inteligente, manteniendo el **blindaje de costo $0** y una escalabilidad industrial.

¿Te gustaría que ahora consolidemos el **`GlobalStatesService`** en Angular para que gestione estos 8 estados de forma centralizada con **Signals**?
