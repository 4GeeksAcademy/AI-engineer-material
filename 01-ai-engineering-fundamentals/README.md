# AI Engineering Fundamentals

Bienvenido/a. En esta clase vas a aprender a trabajar con Inteligencia Artificial de forma deliberada: no como una caja mágica que "a veces funciona", sino como una herramienta que rinde al máximo cuando sabes cómo pedirle las cosas y cómo auditar lo que devuelve.

**Regla de toda la clase:**
> La IA propone, tú verificas.

## Verificación de entorno

Abre tu Codespace de la academia y el panel de Copilot Chat. Pruébalo enviando el siguiente mensaje:

```plaintext
Hola, ¿qué modelo eres?
```

Si te responde, tu entorno está listo para trabajar.

## Prompting: ¿cómo pedir las cosas?

### Un primer experimento

Escríbele a la IA, tal cual, sin agregar nada más:

```plaintext
Escríbeme un mensaje para mi jefe.
```

Lee lo que te devuelve y analiza: ¿te sirve tal cual?, ¿qué le falta?

Probablemente notaste que la IA no sabe quién es tu jefe, el motivo del mensaje ni el tono que usas habitualmente. No es un fallo del modelo: le faltó contexto.

### ¿Por qué pasa esto?

Piensa en un becario en su primer día de trabajo. Si le pides "escríbeme un mensaje para mi jefe" sin más detalles, va a asumir o inventar la información que le falta para poder cumplir la tarea.

La IA funciona bajo un principio similar: no procesa verdades, predice estadísticamente cuál es la respuesta más probable. Cuando no tiene restricciones claras, completa los vacíos por su cuenta.

### Las 5 piezas de un buen pedido

Para obtener respuestas precisas y reproducibles, todo prompt profesional debe incluir:

| Pieza | Función | Ejemplo |
|-------|---------|---------|
| **Rol** | Define el papel o perspectiva que asume la IA. | "Actua como un empleado de atención al cliente." |
| **Contexto** | Describe la situación de fondo y los antecedentes. | "Tengo que avisarle a mi jefe que voy a llegar 20 minutos tarde por un turno médico." |
| **Tarea** | Expresa la acción concreta solicitada. | "Escribime un mensaje corto para enviarle por WhatsApp." |
| **Restricciones** | Establece los límites y lo que se debe evitar. | "Que sea breve, profesional y no suene a excusa." |
| **Formato** | Define la estructura exacta de salida. | "Dame solo el texto del mensaje, sin introducciones ni despedidas." |

### El mismo pedido, bien estructurado:

```plaintext
Actua como un empleado de atención al cliente.
Le tengo que avisar a mi jefe que voy a llegar 20 minutos tarde mañana porque tengo un turno médico.
Escribime un mensaje corto para enviarle por WhatsApp.
Que sea breve y no suene a excusa.
Dame solo el texto del mensaje, sin nada más.
```

Compara el resultado. La diferencia no es que el modelo "mejoró mágicamente", sino que eliminaste la ambigüedad en su contexto.

> **Para quienes ya programan:**
> Aplica esta misma lógica en desarrollo. Compara pedir "Hazme una función de login" frente a un prompt estructurado indicando el rol (desarrollador Backend Python), contexto (e-commerce sin frameworks), tarea (función validar_login), restricciones (solo librería estándar) y formato (solo bloque de código comentado). El salto de calidad es el mismo.

## Verificar antes de confiar

### Diagnóstico de precisión

Preguntale a la IA:

```plaintext
¿En qué año se fundó la Universidad de Buenos Aires?
```

Analizá la respuesta e inmediatamente verificala contrastando con una fuente externa.

A veces la IA da un dato falso con total contundencia sintáctica y tonal. A esto se le llama **alucinación**: el modelo genera texto probabilísticamente coherente, pero tácticamente incorrecto. Ocurre con datos, fechas, conceptos teóricos y código fuente.

> **Para quienes ya programan:**
> Pedile a la IA: "Necesito una función validar_email(texto) en Python que devuelva True o False".
> Recibirás una expresión regular aparentemente perfecta. Sin embargo, al probar casos bordes (como "test @gmail.com" con espacio o "test@gmail" sin TLD), verás que suele fallar. Lo que "se ve bien" a simple vista no siempre es correcto.

### La Checklist de Auditoría

Antes de aceptar cualquier resultado de la IA (texto, cálculo o código), pasalo por estos tres filtros:

- ✓ **¿Lo entendí?** (¿Puedo explicarlo con mis palabras?)
- ✓ **¿Lo verifiqué o ejecuté?** (¿Lo contrasté contra otra fuente o corrí el código?)
- ✓ **¿Probé un caso borde a propósito?** (¿Busqué intencionalmente dónde se rompe?)

## Formatos estructurados

### El problema de la interfaz humana vs. máquina

Si necesitas procesar esta información mediante código:

> "El pedido de Ana López llegó el 12/09 por $4500, todavía no fue despachado."

Una oración en texto plano no es eficiente para un sistema informático. Los datos necesitan una estructura estandarizada. JSON (JavaScript Object Notation) es la forma estándar de estructurar datos mediante pares clave-valor para que puedan ser leídos por cualquier software.

### Extracción de datos

Prueba la extracción directa:

```plaintext
Extrae del siguiente texto un JSON con las claves: cliente, fecha, monto, despachado (boolean). Devuelve solo el JSON.

Texto: "El pedido de Ana López llegó el 12/09 por $4500, todavía no fue despachado."
```

Obtendrás una estructura limpia:

```json
{
  "cliente": "Ana López",
  "fecha": "12/09",
  "monto": 4500,
  "despachado": false
}
```

### Provocar el fallo (Edge Case)

Ahora repite la prueba eliminando el monto del texto de origen:

```plaintext
Extrae del siguiente texto un JSON con las claves: cliente, fecha, monto, despachado (boolean). Devuelve solo el JSON.

Texto: "El pedido de Ana López llegó el 12/09, todavía no fue despachado."
```

Observa la respuesta. Es muy probable que la IA invente un número para completar el campo monto. Para evitarlo en entornos de producción, la regla de negocio debe estar en el prompt: 

> "Si un dato no está en el texto, asigna null. No inventes información."

## Eficiencia de tokens y contexto

Los modelos de lenguaje no leen palabras ni caracteres individuales; procesan tokens, que son representaciones numéricas de fragmentos de texto (subwords). La mayoría de los LLMs utilizan un algoritmo llamado Byte-Pair Encoding (BPE) para traducir el texto plano a números enteros y viceversa.

```plaintext
Texto: "Programación en Python"
Tokens: ["Program", "ación", " en", " Py", "thon"]
IDs:     [ 18243,     9412,   412,   891,  4120 ]
```

### Factores que distorsionan el conteo de tokens

- **El idioma importa:** La regla estimada de $1 \text{ token} \approx 0.75 \text{ palabras}$ aplica principalmente al inglés. En español, debido a las conjugaciones y acentuaciones, un mismo párrafo puede requerir entre un 20% y 50% más de tokens.
- **El "costo" de la estructura:** Formatos como JSON, etiquetas XML, sangrías de código y caracteres especiales consumen tokens de control.
- **Efectos colaterales de la tokenización:** ¿Por qué a un LLM le cuesta contar cuántas veces aparece una letra en una palabra? Porque el modelo no ve letras aisladas, sino el ID entero del subfragmento.

### Anatomía del consumo de tokens

No todos los tokens impactan de la misma manera en el rendimiento y los costos:

- **Tokens de Entrada (Prompt / Input Tokens):** Es todo lo que el modelo recibe antes de generar una respuesta: tu pregunta, instrucciones, documentos, código y contexto anterior. Ejemplo: si envias "Explicame este código" junto con 500 líneas de código, el modelo tiene que procesar todo ese contenido antes de responder. Los tokens de entrada se procesan de forma paralela, por lo que generalmente tienen menor costo económico y menor impacto en la latencia que los tokens de salida.
- **Tokens de Salida (Completion / Output Tokens):** Es el texto que el modelo genera. Se calculan de forma autoregresiva (token por token de forma secuencial), lo que genera la latencia de respuesta. Suelen costar entre 3 y 4 veces más que los de entrada.
- **Prompt Caching:** Algunos proveedores pueden reconocer partes de un prompt que se repiten entre distintas solicitudes y reutilizar ese procesamiento. **Ejemplo:** imagina que todas tus consultas incluyen las mismas 20 páginas de documentación y solo cambia tu pregunta final. Si el proveedor ofrece prompt caching, esa parte repetida puede aprovecharse nuevamente en lugar de procesarse como si fuera completamente nueva.

### La Ventana de Contexto y la "Atención"

Cada modelo cuenta con una Ventana de Contexto máxima (ej. 128k tokens). Sin embargo, tener una ventana grande no significa que el modelo procese todo con la misma agudeza:

- **Pérdida en el medio (Lost in the Middle):** Los LLMs tienden a prestar más atención a la información ubicada al principio (System Prompt) y al final (último mensaje) del contexto, degradando su precisión para recuperar datos ubicados en el centro de conversaciones muy extensas.
- **Acumulación oculta:** Cada mensaje enviado en un chat no viaja solo; vuelve a enviar todo el historial acumulado, multiplicando el consumo de tokens de entrada en cada turno.

### Tres hábitos fundamentales de gestión de contexto

1. **Una tarea por conversación:** Evita acumular discusiones no relacionadas en la misma sesión. Una conversación limpia garantiza una atención máxima del modelo en el problema presente.
2. **Contexto quirúrgico:** Provee únicamente la documentación, logs o código necesarios para la tarea actual. Evita pegar archivos enteros de código si solo vas a refactorizar una función.
3. **Reset e iteración estratégica:** Cuando un hilo se contamine con intentos fallidos o instrucciones contradictorias, no continúes corrigiendo en la misma ventana. Abre un chat nuevo, sintetizá el estado actual del problema y utilizá esa síntesis como nuevo punto de partida.

## Modos de trabajo con Agentes

Según la complejidad de la tarea, la interacción con la IA adopta distintos niveles de autonomía:

| Modo de trabajo | Cuándo usarlo | Ejemplo |
|-----------------|---------------|---------|
| **Chat Simple** | Para explorar ideas, entender conceptos o resolver dudas puntuales. | Entender el motivo de un mensaje de error desconocido. |
| **Edición Guiada** | Para cambios pequeños y acotados donde tu apruebas la modificación exacta. | Corregir un typo o refactorizar un método corto. |
| **Agente Autónomo** | Para tareas complejas multipaso o que involucran múltiples archivos. | Implementar una funcionalidad completa que requiere crear modelos, rutas y tests. |

### La conexión sistémica

Un Agente de IA no es un paradigma mágico diferente: es un loop automatizado de los pasos que aprendiste hoy:

$$\text{Planear} \longrightarrow \text{Ejecutar} \longrightarrow \text{Observar / Auditar} \longrightarrow \text{Corregir} \longrightarrow \text{Repetir}$$

Dominar la evaluación manual e individual de prompts y respuestas es la condición previa para poder supervisar el trabajo de un agente cuando este entra en un ciclo de ejecución automática.

