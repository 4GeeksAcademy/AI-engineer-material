# Spec-Driven Development en el Dashboard Financiero

> En este proyecto **no construyes** el dashboard. Escribes la **especificación** tan clara y tan comprobada que otra persona (o un coding agent) pueda construirlo sin hacerte una sola pregunta.


Aquí practicas como:

1. Convertir un pedido de negocio ambiguo en requisitos precisos.
2. Comprobar cada afirmación contra la **API real**, no contra lo que "suena bien".
3. Decidir tú lo que el pedido dejó abierto.
4. Escribir una spec que se sostenga por sí sola.

**Tu rol:** eres el arquitecto y el verificador. El agente investiga y redacta. Tú decides y compruebas.

## Glosario express

| Palabra | Significa |
|---|---|
| **API** | Un puente que permite que dos programas se hablen y se pasen información. |
| **Endpoint** | Una dirección concreta de la API (ej. `/api/metrics/alerts`). |
| **Parámetro** | Un dato extra que le das a la API para decirle exactamente qué necesitas. (ej. `?threshold=0.3`). |
| **JSON** | El formato de la respuesta: pares `nombre: valor`. |
| **OpenAPI / Swagger** | El manual de instrucciones de una API: explica qué puedes pedir, cómo pedirlo y qué recibirás. |
| **Tipo (TypeScript)** | La descripción de qué forma tiene un dato. |
| **Componente** | Una pieza de la pantalla (una tabla, un filtro, un gráfico). |
| **Props** | Los datos que recibe un componente para dibujarse. |
| **Spec** | Un documento tan claro que se puede construir sin preguntar. |
| **Commit** | Una "foto" guardada de tu trabajo, con un mensaje que explica qué cambió. |


### La única sintaxis de TypeScript que necesitas

Este ejemplo **no es del proyecto**; solo sirve para que reconozcas las piezas:

```ts
interface Persona {
  /** Nombre completo. Ej: "Ana Pérez" */
  nombre: string;
  /** Opcional (por el signo "?"). Formato YYYY-MM-DD */
  fechaNacimiento?: string;
}
```

- `interface` describe la forma de un dato.
- `string` es texto y `number` es un número.
- `?` significa "opcional".
- `/** ... */` es un comentario que explica el campo. Aquí se llama **JSDoc**.
- `'a' | 'b'` significa "solo puede ser `a` o `b`".


## Reglas del proyecto (léelas antes de empezar)

- **`/docs` no es una carpeta.** Es la página de documentación interactiva de la API: `http://localhost:8000/docs`. Si trabajas en Codespaces, abre la pestaña **PORTS**, abre el puerto 8000 en el navegador y agrega `/docs` al final.
- **Explorar la API está permitido y es obligatorio** (Swagger, `curl`). Lo que **no** haces es escribir código de producto: nada de componentes React, nada de `fetch` en el frontend, nada de cambios en el backend.
- **Ninguna afirmación sin evidencia.** Cada campo y cada parámetro tiene que poder rastrearse hasta una respuesta real de la API.
- **Una sola fuente de verdad.** Los tipos viven en `api-types.ts` y `param-types.ts`. El README **los enlaza**, no los copia.
- **El pedido original del PM no se edita.** Es la referencia contra la que mides tu spec.

### Marcas de verificación

Usarás estas tres marcas en todo el proyecto:

| Marca | Significa |
|---|---|
| ✅ | Lo vi en una respuesta real de la API o en `openapi.json`. |
| ❌ | Lo comprobé y es incorrecto (el pedido dice una cosa y la API otra). |
| ❓ | No pude confirmarlo todavía. |

### Qué compone tu entrega

```
frontend/
└── specs/
    ├── pm-brief.md       # Lo que se pidió (texto original, no se edita)
    ├── verification.md   # Lo que es verdad en la API + tus decisiones
    ├── api-types.ts      # Tipos de las respuestas
    ├── param-types.ts    # Tipos de los parámetros
    ├── components.md     # Especificación de componentes y estados
    └── README.md         # Contrato de datos y casos límite
```

Cada archivo responde una pregunta distinta:

- `pm-brief.md` → ¿qué **pidieron**?
- `verification.md` → ¿qué es **verdad** y qué **decidimos**?
- `api-types.ts`, `param-types.ts` → ¿qué **forma** tienen los datos?
- `components.md` → ¿qué se va a **construir**?
- `README.md` → ¿cómo se **conecta** todo?


## Preparación

En la terminal del proyecto:

```bash
git switch -c feature/frontend-specs
mkdir -p frontend/specs
docker compose up --build
```

- `git switch -c ...` crea tu rama de trabajo y te mueve a ella.
- `mkdir -p frontend/specs` crea la carpeta donde irá todo.
- `docker compose up --build` levanta el frontend y el backend. **Déjalo corriendo** y abre una segunda terminal para el resto.

Comprueba que `http://localhost:8000/docs` carga una página con una lista de endpoints de colores.

**Prompt de arranque** (en tu coding agent):

```
Lee AGENTS.md, la carpeta memory-bank/ y .agents/rules/ (si existen).
Resúmeme en 5 líneas: qué es este proyecto, qué tecnologías usa y qué
reglas debo respetar. No modifiques ningún archivo.
```

✋ **Tu verificación:** el resumen debe mencionar React, TypeScript y FastAPI. Si `memory-bank/` o `.agents/rules/` no existen, el agente lo dirá: anótalo y sigue.

🧠 **PREGUNTA:** sin mirar tus notas, ¿para qué servía el memory-bank del proyecto anterior? Una spec se construye sobre contexto, y por eso el agente lo lee antes de escribir nada.


# FASE 1: Registrar el pedido, auditar la API y decidir

**Objetivo:** capturar lo que pidió el PM sin perder nada, descubrir qué dice la API real y resolver los desajustes **antes** de escribir un solo tipo.

### 1. Guarda el pedido original, sin resumir

Copia las 3 solicitudes del PM (Funcionalidad 1, 2 y 3) del enunciado del proyecto.

```
Crea frontend/specs/pm-brief.md con el texto EXACTO que pego abajo.
No resumas, no reformules, no corrijas nada. Solo agrega al inicio este
encabezado y esta nota:

# Pedido original del PM
> Fuente original. No editar. Las decisiones y correcciones van en verification.md.

[PEGA AQUÍ LAS 3 SOLICITUDES DEL PM, TAL CUAL]
```

**¿Por qué textual?** Un resumen pierde justo lo ambiguo: si el agente resume, también resume los huecos y ya no podrás verlos. Además, en una sesión nueva del agente, este archivo es lo único que le dice qué se pidió.

### 2. Predice antes de mirar

Crea `frontend/specs/verification.md` con esta plantilla:

```markdown
# Verificación

## Mis dudas antes de mirar la API
- Funcionalidad 1:
- Funcionalidad 2:
- Funcionalidad 3:

## Afirmaciones verificadas
| Afirmación | Fuente | Estado (✅ ❌ ❓) |
|---|---|---|

## Decisiones
| Duda | Decisión | Quién la confirma (yo / el PM) |
|---|---|---|
```

Rellena **solo** la primera sección: escribe **2 dudas por funcionalidad** leyendo el pedido del PM. Por ejemplo: *"¿Qué pasa si solo relleno la fecha de inicio?"*


### 3. Explora la API a mano

En `http://localhost:8000/docs`:

1. Busca `GET /api/metrics/facets` y haz clic para desplegarlo.
2. Pulsa **Try it out** y luego **Execute**.
3. Mira **Response body**: ese JSON es la realidad.
4. Repite con `alerts` (`threshold` = `0.3`) y con `categories/top` (`operation_type` = `income`, `limit` = `5`).
5. En cada uno, fíjate en los nombres de los parámetros y de los campos de la respuesta.

### 4. Explora la API con el agente

```
El backend corre en http://localhost:8000. Lee http://localhost:8000/openapi.json
y haz peticiones reales con curl a estos endpoints:
- GET /api/metrics/facets
- GET /api/metrics/alerts?threshold=0.3
- GET /api/metrics/categories/top?operation_type=income&limit=5

Completa la sección "Afirmaciones verificadas" de frontend/specs/verification.md.
Una fila por cada parámetro y por cada campo de respuesta: nombre, tipo, si es
obligatorio y valores válidos. Marca ✅ SOLO lo que viste en una respuesta real
o en openapi.json. Marca ❓ lo que no puedas confirmar. No inventes campos.
No escribas tipos ni specs todavía.
```

🧠 **Reto:** pídele al agente en un mensaje aparte:

```
Añade a la tabla UN campo falso que parezca real, sin decirme cuál.
```

Luego búscalo comparando con Swagger. Aprender a detectar un error se queda en la memoria mucho más que ver un acierto.

### 5. Contrasta el pedido con la realidad

```
Lee frontend/specs/pm-brief.md y frontend/specs/verification.md.
Hazme una tabla: Lo que dice el PM | Lo que dice la API | Por qué importa.
Responde con ✅/❌/❓ y evidencia para estas 6 preguntas:
1. ¿El endpoint /alerts acepta parámetros de fecha?
2. ¿Qué parámetro o campo distingue B2B de B2C?
3. ¿La API devuelve el total de ingresos del grupo o solo el top 5?
4. ¿El incremento de una alerta viene como ratio (0.45) o como porcentaje (45)?
5. ¿Qué responde la API si threshold está fuera de 0.01–1.0 (error o ajuste)?
6. ¿Qué parámetros de fecha acepta el endpoint de métricas existente y cómo se llaman?
No supongas: si no puedes comprobarlo, márcalo ❓.
```

✋ **Tu verificación:** para cada fila, abre Swagger y comprueba **al menos tres** con tus propios ojos. Si el agente dice ✅, tú tienes que poder señalar dónde lo viste.

### 6. Decide tú

Cada ❌ y cada ❓ es una pregunta que el agente habría resuelto en silencio. Ahora la resuelves tú:

```
Para cada ❌ y ❓ de la tabla anterior, propón una decisión con este formato:
Duda | Opciones (2 como máximo) | Recomendada | Por qué.
No decidas por mí: presenta las opciones. Cuando yo elija, escribe la elección
en la sección "Decisiones" de verification.md.
```

**Decisiones base que puedes adoptar** (no dependen de la API):

- **Una sola fecha rellena:** el filtro queda abierto por el otro lado. Solo inicio significa "desde"; solo fin significa "hasta".
- **Fecha de inicio mayor que la de fin:** se avisa en pantalla y no se envía la petición.
- **Rango de fechas en cada página:** independiente, usando el mismo componente de filtro.

En la columna **"Quién la confirma"** marca si la decisión es tuya (técnica) o del PM (de negocio). Lo que no puedas decidir tú queda como pregunta para el PM.


## Commit de la Fase 1

```bash
git add frontend/specs/pm-brief.md frontend/specs/verification.md
git commit -m "docs(phase-1): record PM brief and verify API contract against live /docs"
```


# FASE 2: Tipos TypeScript

**Objetivo:** traducir lo que **comprobaste** (no lo que supones) en tipos estrictos y documentados.

**¿Por qué tipos?** Un tipo es una especificación que un programa puede revisar. Cuando el agente construya la pantalla, el tipo le dirá exactamente qué campos existen y de qué clase son.

## Prompt

```
Lee frontend/specs/pm-brief.md y frontend/specs/verification.md antes de escribir.

1) Crea frontend/specs/api-types.ts con interfaces TypeScript estrictas:
   FacetsResponse, AlertEntry, AlertsResponse, CategoryEntry, TopCategoriesResponse.
   Reglas:
   - Usa SOLO campos marcados ✅ en verification.md.
   - Prohibido any y object.
   - Cada propiedad lleva un comentario JSDoc con: qué significa, valores válidos,
     formato si aplica (ej. YYYY-MM-DD) y una línea @see con el endpoint de origen.
   - Si un campo es ❓, no lo incluyas: déjalo como comentario // TODO.

2) Crea frontend/specs/param-types.ts con:
   - DateRangeFilter: fecha de inicio y fecha de fin, ambas OPCIONALES, tipo string,
     JSDoc con formato YYYY-MM-DD. Usa los nombres de parámetro reales de openapi.json.
   - AlertsParams: threshold + DateRangeFilter (extiende o incluye). Si /alerts no
     acepta fechas según verification.md, aplica la decisión que tomé allí.
   - TopCategoriesParams: operation_type (unión literal con los valores reales de la
     API, no un string suelto), limit + DateRangeFilter, y el parámetro de línea de
     negocio SOLO si verification.md confirma que existe.
   - Si el endpoint de métricas existente acepta fechas, agrega también MetricsParams
     que incluya DateRangeFilter.
```

## ✋ Tu verificación

No tienes que escribir TypeScript. Tienes que **comprobar**:

1. Elige **un campo** de `api-types.ts` y búscalo en el JSON real de Swagger. ¿Existe? ¿Mismo nombre? ¿Mismo tipo?
2. Elige **un parámetro** de `param-types.ts` y comprueba en Swagger que se llama exactamente así.
3. Busca la palabra `any` en los dos archivos. No debe aparecer.
4. Cada propiedad tiene un comentario JSDoc con significado, valores válidos y formato.
5. `AlertsParams` y `TopCategoriesParams` incluyen a `DateRangeFilter`.

Luego comprueba que el código compila:

```bash
cd frontend
npx tsc --noEmit --strict specs/api-types.ts specs/param-types.ts
```

> Si no hay salida, no hay errores. Si aparecen errores, pídele al agente que los corrija. Ojo: **que compile no significa que sea correcto**. `tsc` solo comprueba que los tipos sean coherentes entre sí, no que coincidan con la API. Eso solo lo garantiza tu verificación contra Swagger.

🧠 **PREGUNTA:** ¿qué diferencia hay entre "el código compila" y "la spec es correcta"?

## Commit de la Fase 2

```bash
git add frontend/specs/api-types.ts frontend/specs/param-types.ts
git commit -m "docs(phase-2): add API response and param types verified against OpenAPI"
```


# FASE 3: Especificar los componentes

**Objetivo:** decir exactamente qué se dibuja, con qué datos y qué ve el usuario en **cada situación**, incluidas las incómodas.

**¿Por qué estados?** Los agentes rellenan con valores por defecto los estados que no escribes (vacío, error, dato incompleto). Y ahí nacen los bugs de pantalla.

## Prompt

```
Lee frontend/specs/pm-brief.md, verification.md, api-types.ts y param-types.ts
antes de escribir.

Crea frontend/specs/components.md. Para cada una de las 3 funcionalidades usa
EXACTAMENTE esta estructura:
1. Propósito (una frase)
2. Componentes (nombres en PascalCase)
3. Props de cada componente: tabla con nombre | tipo (usa los tipos de
   api-types.ts y param-types.ts) | obligatoria sí/no | descripción
4. Estados: tabla con cargando | vacío | error | dato parcial o inválido →
   qué ve exactamente el usuario en cada uno
5. Decisiones aplicadas (de verification.md)

No escribas código React ni JSX. Cubre de forma explícita:
- Funcionalidad 1: qué pasa si solo hay una fecha rellena; cómo se muestra el
  rango disponible (desde FacetsResponse).
- Funcionalidad 2: las 4 columnas con su tipo de dato y su formato de visualización;
  estado vacío con un mensaje explícito (por ejemplo: "No se detectaron anomalías
  para el umbral X"); qué pasa si threshold está fuera de 0.01–1.0.
- Funcionalidad 3: layout de dos paneles paralelos; qué renderiza cada panel cuando
  su lista top-5 viene vacía; qué muestra el gráfico comparativo y qué representa
  cada uno de sus dos puntos de datos.
```

## ✋ Tu verificación

Revisa tu `components.md` como si fueras quien lo va a construir:

- [ ] Cada componente tiene nombre, props y tipos de esas props.
- [ ] Los tipos de las props existen de verdad en `api-types.ts` o `param-types.ts`.
- [ ] La tabla de estados no tiene celdas vacías en ninguna funcionalidad.
- [ ] El estado vacío de la tabla de anomalías está escrito con un mensaje concreto.
- [ ] Está escrito qué pasa con **una sola** fecha rellena.
- [ ] **Ambos** paneles B2B y B2C dicen qué muestran cuando su top-5 viene vacío.
- [ ] Puedes explicar de dónde sale cada dato del gráfico comparativo (¿lo devuelve la API o hay que calcularlo?).
- [ ] No hay código React ni JSX.

🧠 **Revisión cruzada:** si tienes un compañero o compañera, intercambien `components.md` y busquen **un estado que falte**. Si trabajas solo, relee tu archivo en voz alta como si fueras la persona que lo va a programar y marca cada frase que te haga dudar.

## Commit de la Fase 3

```bash
git add frontend/specs/components.md
git commit -m "docs(phase-3): define component layout, props, and edge case UI behaviors"
```


# FASE 4: Contrato de datos, prueba en frío y cierre

**Objetivo:** reunir todo en un documento de entrada y **comprobar** que otra persona (o agente) puede usarlo sin ayudarte.

### 1. Redacta el README del contrato

```
Lee frontend/specs/pm-brief.md, verification.md, api-types.ts, param-types.ts y
components.md antes de escribir.

Crea frontend/specs/README.md como punto de entrada para una sesión nueva del
agente. Por cada funcionalidad incluye:
- Endpoint(s) con la ruta verificada.
- Enlaces a los tipos en api-types.ts y param-types.ts (enlaza, no copies).
- Parámetros con sus valores válidos y restricciones.
- Al menos 2 casos límite y qué debe mostrar la UI en cada uno.
- Referencia al requisito de pm-brief.md que cubre.

Sugerencias de casos límite:
- Funcionalidad 1: solo una fecha, inicio mayor que fin, rango fuera del disponible.
- Funcionalidad 2: threshold fuera de rango, sin alertas, períodos iniciales sin
  3 períodos previos para calcular la media móvil.
- Funcionalidad 3: menos de 5 categorías, grupo sin ingresos, rango sin datos.
```

### 2. La prueba en frío

Abre una **sesión nueva** del agente (así no recuerda nada de lo que hablaron) y pega:

```
Actúa como el desarrollador que implementará esto. Lee la carpeta frontend/specs/
completa. No implementes nada.
1. Lista los requisitos de pm-brief.md que NO estén cubiertos por la spec.
2. Lista las preguntas, ambigüedades y contradicciones que te impedirían
   construir las 3 funcionalidades sin consultarme.
```

**Cómo leer el resultado:** cada pregunta que devuelva es un defecto de tu spec. Cada requisito sin cubrir es una omisión, y las omisiones son los errores más difíciles de ver, porque no hay nada escrito que parezca mal.

Corrige los defectos y repite la prueba hasta que devuelva pocas preguntas o ninguna.

### 3. Compilación final

```bash
cd frontend
npx tsc --noEmit --strict specs/api-types.ts specs/param-types.ts
```

## ✋ Autoevaluación final

- [ ] Cada tipo y parámetro puede rastrearse hasta una fila ✅ de `verification.md`.
- [ ] No hay `any` ni `object`.
- [ ] `DateRangeFilter` tiene ambos campos opcionales, de tipo `string`, con JSDoc `YYYY-MM-DD`.
- [ ] `AlertsParams` y `TopCategoriesParams` incluyen a `DateRangeFilter`.
- [ ] `components.md` nombra cada componente, sus props con tipo y su renderizado condicional.
- [ ] Están especificados: el estado vacío de anomalías, el comportamiento con una sola fecha y el top-5 vacío de **ambos** paneles.
- [ ] El README cubre las 3 funcionalidades con endpoints, tipos, parámetros y al menos 2 casos límite cada una.
- [ ] La prueba en frío devuelve pocas o ninguna pregunta.
- [ ] `tsc` compila sin errores.
- [ ] Todo está en la rama `feature/frontend-specs`, con commits claros.
- [ ] No hay componentes React, llamadas `fetch` ni cambios en el backend.

## Commit de la Fase 4

```bash
git add frontend/specs/README.md
git commit -m "docs(phase-4): add data contract README and cold-read fixes"
```

## Cierre: lo que te llevas

Completa por escrito estas dos frases:

1. *"Lo que esta spec me obligó a decidir y no había visto es…"*
2. *"La próxima vez que un agente me diga que un campo existe, voy a…"*


## Si algo no funciona

| Problema | Qué hacer |
|---|---|
| `http://localhost:8000/docs` no carga | Revisa que `docker compose up --build` siga corriendo y sin errores. En Codespaces, abre el puerto 8000 desde la pestaña **PORTS**. |
| El agente no puede hacer `curl` | Pídele que lea el código del backend para sacar las rutas y los esquemas, y marca lo que no pudo probar en vivo como ❓. |
| `tsc` da un error que no entiendes | Copia el error y pídele al agente: *"Explícame este error en palabras simples y propón el arreglo mínimo."* |
| Algo en el pedido del PM no se puede comprobar | Déjalo como ❓ y escríbelo como pregunta para el PM. Eso también es un buen resultado. |

## Si estás empezando desde cero

No necesitas saber programar para hacer bien este proyecto. Lo que se evalúa es tu **razonamiento de verificación**:

- En la **Fase 1** haces todo tú: comparar, comprobar y decidir.
- En las **Fases 2, 3 y 4** el agente escribe y tú verificas con las listas de comprobación de cada fase. No hace falta que entiendas cada línea de TypeScript. Sí hace falta que puedas responder:
  - ¿Ese campo existe en Swagger?
  - ¿Ese estado está especificado?
  - ¿Hay al menos 2 casos límite?
  - ¿Puedo explicar de dónde sale cada dato?

Si puedes responder esas cuatro preguntas, estás haciendo Spec-Driven Development.
