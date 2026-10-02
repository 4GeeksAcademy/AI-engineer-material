# Spec-Driven Development (SDD)


## El problema: ¿por qué necesitamos especificar?

Antes de hablar de IA, piensa en cómo se desarrolla software cuando trabajan solo personas. Casi siempre existe, de forma más o menos formal, esta cadena:

```text
problema → requisitos → diseño → implementación → pruebas → verificación
```

Alguien entiende un problema, decide qué debe hacer el sistema, lo diseña, lo construye y comprueba que funcione como se esperaba. Cuando esa cadena se rompe, aparecen los problemas conocidos: 

- se construye algo que nadie pidió 
- se descubre tarde que faltaba un requisito o dos personas entienden el mismo pedido de forma distinta.

Por eso, a lo largo de la historia, surgieron muchas formas de comunicar qué debe construirse: documentos de requisitos, casos de uso, historias de usuario, criterios de aceptación, pruebas escritas antes del código.


Imagina que alguien de la academia te dice:

> *"Necesitamos mejorar el sistema de tickets."*

Una persona del equipo podría responder con preguntas: ¿mejorar qué?, ¿para quién?, ¿cómo sabremos que mejoró? Un agente de IA, en cambio, puede **empezar a trabajar de inmediato**, rellenando con suposiciones todo lo que no se dijo.

Ahí aparece la pregunta que organiza esta clase:

> **¿Qué cambia cuando parte del trabajo empieza a realizarlo un agente de IA?**



## ¿Qué es SDD?

**Spec-Driven Development (desarrollo guiado por especificaciones)** es una forma de trabajar en la que la especificación es la **referencia explícita** durante todo el desarrollo: sirve para comunicar qué queremos construir, orienta el trabajo y permite comprobar si el resultado cumple lo esperado.

No consiste solo en "escribir un documento antes de programar". Lo importante es que la spec **se usa**: el plan se deriva de ella, la implementación se guía por ella y la verificación se hace contra ella.

**¿Qué es una especificación?** Es una descripción de **qué debe hacer el sistema y bajo qué condiciones**, con la precisión suficiente para que otra persona (o un agente) pueda implementarlo y para que luego se pueda comprobar si se cumplió.

**¿Por qué importa con agentes?** Un agente puede investigar el código, planificar, modificar varios archivos y ejecutar pruebas. Esa autonomía es valiosa, pero tiene un costo: si la intención no está clara, el agente la completa con suposiciones, y el resultado puede verse bien y no ser lo que necesitabas. La spec reduce esas suposiciones y deja un punto de referencia para revisar.

### Spec, prompt e implementación no son lo mismo

| | **Prompt** | **Spec** | **Implementación** |
|---|---|---|---|
| **Qué es** | Una instrucción para el agente en un momento dado | Descripción del comportamiento esperado y sus condiciones | El código que lo hace realidad |
| **Responde a** | "¿Qué le pido ahora?" | "¿Qué debe cumplir el sistema?" | "¿Cómo se construye?" |
| **Duración** | Una conversación | Vive con el proyecto | Cambia con frecuencia |
| **En el caso de los tickets** | "Implementa la regla de tickets atrasados" | "Un ticket sin respuesta del equipo tras 48 horas debe marcarse como atrasado" | La función que calcula y marca el estado |

> Un prompt puede **tomar** parte de la spec, pero la spec es el artefacto estable del que se derivan los prompts.



## Desarrollo tradicional vs. SDD

SDD no reemplaza las prácticas tradicionales. Las **conserva**: seguimos entendiendo el problema, definiendo requisitos, diseñando, implementando, probando, verificando e iterando.

Lo que cambia es **quién hace qué** y **qué tan explícito debe estar lo que esperamos**:

| | **Desarrollo tradicional** | **SDD con agentes de IA** |
|---|---|---|
| **Quién implementa** | Personas, que pueden preguntar, discutir y usar su criterio | Un agente que ejecuta rápido y, si falta información, supone |
| **Intención y requisitos** | A menudo viven en conversaciones, tickets o en la cabeza del equipo | Necesitan estar escritos y accesibles para el agente |
| **Comportamiento esperado** | Puede quedar implícito entre personas que se conocen | Se define de forma explícita y verificable |
| **Verificación** | Pruebas y revisión humana | Pruebas y revisión humana, **contra la spec**, porque el volumen de código generado es mayor |

Las preguntas son las mismas; lo que sube es la importancia de **escribir las respuestas**.

> **Idea clave:** SDD no elimina el proceso de desarrollo. Hace que la intención y el comportamiento esperado estén definidos de forma explícita y puedan usarse como referencia mientras trabaja el agente.


## SDD y Context Engineering

Son dos ideas que se complementan, pero responden preguntas distintas:

> **Context Engineering → ¿qué necesita saber el agente para trabajar?**
> **SDD → ¿qué queremos que se construya y cómo describimos el comportamiento esperado?**

En el caso de los tickets:

```text
CONTEXTO (el entorno en el que se trabaja)
- La aplicación usa Node.js y PostgreSQL
- Las rutas siguen una estructura determinada
- Hay convenciones de nombres y estilo
- Ya existe un sistema de usuarios con roles

SPEC (lo que se quiere construir)
- Objetivo: que ningún ticket quede sin atención
- Comportamiento: los tickets sin respuesta se marcan como atrasados
- Restricciones: no modificar el sistema de usuarios
- Criterios de aceptación: cómo comprobamos que funciona
```

El contexto ayuda al agente a **trabajar bien dentro de tu proyecto**. La spec le dice **qué debe lograr**. Con buen contexto y sin spec, el agente conoce el terreno pero no el destino. Con spec y sin contexto, conoce el destino pero puede desconocer el terreno.


## ¿Qué contiene una buena spec?

No existe una plantilla universal obligatoria. Lo útil es entender **para qué sirve cada componente**:

| Componente | Para qué sirve |
|---|---|
| **Objetivo** | Explica el porqué y orienta las decisiones no previstas |
| **Contexto** | Aporta lo necesario para entender la situación |
| **Alcance** | Define qué entra y, sobre todo, **qué no entra** |
| **Requisitos** | Enumeran lo que el sistema debe cumplir |
| **Comportamiento esperado** | Describe qué ocurre en cada situación relevante |
| **Restricciones** | Marcan los límites técnicos o de negocio |
| **Criterios de aceptación** | Permiten comprobar si está terminado |
| **Casos límite** | Cubren lo raro: sin datos, valores extremos, fechas en el borde |

Una spec pequeña puede tener solo algunos de estos elementos. Una spec para algo crítico, más. La regla práctica: **incluye lo que, si faltara, obligaría al agente a adivinar algo importante**.


## Tipos de SDD (solo como contexto)

"SDD" es un término todavía en evolución: distintas comunidades y herramientas lo usan de formas diferentes. Conviene distinguir dos niveles:

- **El concepto general**: usar una especificación como referencia explícita durante el desarrollo. Es lo que aprendes en esta clase y no depende de ninguna herramienta.
- **Metodologías o herramientas concretas**: formas específicas de organizar los documentos, los pasos y los comandos. Cambian rápido y aquí no las estudiamos.

Una clasificación que circula con frecuencia distingue tres grados de uso de la spec:

| Enfoque | Idea |
|---|---|
| **Spec-first** | La spec se escribe primero y guía la tarea actual |
| **Spec-anchored** | La spec se mantiene viva después de la tarea y se usa para evolucionar el sistema |
| **Spec-as-source** | La spec es el artefacto principal y el código se genera a partir de ella |

Es una forma de ordenar las ideas, **no una taxonomía oficial**. Para empezar en proyectos pequeños, lo más práctico es *spec-first*, y mantener la spec actualizada cuando el comportamiento cambia.


## ¿Cómo se trabaja con SDD?

Este flujo es general e independiente de cualquier herramienta:

```text
 1. Comprender el problema
        ↓
 2. Definir la intención
        ↓
 3. Especificar requisitos
        ↓
 4. Definir el comportamiento esperado
        ↓
 5. Establecer restricciones y criterios de aceptación
        ↓
 6. Revisar la spec
        ↓
 7. Planificar
        ↓
 8. Implementar
        ↓
 9. Probar
        ↓
10. Verificar contra la spec
        ↓
11. Iterar
```

Los pasos 1 a 6 definen **qué** construir; el 7, **cómo** abordarlo; del 8 al 10 se construye y se comprueba; el 11 cierra el ciclo. Si al verificar algo no cumple, se vuelve atrás: a veces hay que corregir el código, y otras, la spec.

### ¿Qué puede hacer el agente en este flujo?

| El agente puede ayudar a... | La persona sigue siendo responsable de... |
|---|---|
| Investigar el código existente | Definir la intención y decidir qué se construye |
| Detectar ambigüedades y proponer preguntas | Resolver esas ambigüedades |
| Proponer un plan | Revisar y aprobar el plan |
| Implementar y escribir pruebas | Entender lo que se implementó |
| Ejecutar pruebas y revisar | Validar que el resultado sea correcto |

> **Idea clave:** el agente puede ayudar a investigar, proponer, planificar, implementar, probar y revisar; la persona sigue siendo responsable de definir la intención y validar que el resultado sea correcto. El agente colabora, **no es la autoridad**.


## El caso completo: de un pedido ambiguo a una verificación

Recorramos el caso de los tickets de principio a fin.

### Paso 1: el pedido

> *"Necesitamos mejorar el sistema de tickets."*

Es ambiguo porque no dice **qué** mejorar (¿velocidad?, ¿orden?, ¿seguimiento?), **para quién** (¿estudiantes o soporte?) ni **cómo sabremos que mejoró**. Con este pedido, un agente podría rediseñar la interfaz, agregar notificaciones o cambiar la base de datos, y todo "sonaría" a mejora.

### Paso 2: la intención y los requisitos

Conversando con el equipo de soporte aparece el verdadero problema: *los tickets se pierden y nadie sabe cuáles llevan días sin respuesta.*

- **Intención:** que ningún ticket quede sin atención durante demasiado tiempo.
- **Requisitos:**
  - Cada ticket tiene un estado: *abierto*, *en progreso*, *resuelto* o *atrasado*.
  - Un ticket sin respuesta del equipo durante 48 horas debe marcarse como *atrasado*.
  - El equipo debe poder ver la lista de tickets atrasados.
  - Los estudiantes solo ven sus propios tickets.

### Paso 3: la spec

```markdown
# Spec: Detección de tickets atrasados

## Objetivo
Que el equipo de soporte identifique los tickets sin respuesta para atenderlos
antes de que el estudiante quede esperando días.

## Alcance
Dentro: marcar tickets atrasados y mostrarlos al equipo.
Fuera: notificaciones por correo, cambios en el sistema de usuarios,
rediseño de la interfaz.

## Comportamiento esperado
- Un ticket en estado "abierto" o "en progreso" sin respuesta del equipo
  durante 48 horas consecutivas pasa a "atrasado".
- Si el equipo responde, el ticket deja de estar atrasado y vuelve a "en progreso".
- Los tickets "resueltos" nunca se marcan como atrasados.

## Restricciones
- No modificar la estructura de usuarios existente.
- No agregar dependencias nuevas.

## Criterios de aceptación
1. Dado un ticket abierto hace 49 h sin respuesta, cuando corre la revisión,
   entonces su estado es "atrasado".
2. Dado un ticket abierto hace 47 h sin respuesta, cuando corre la revisión,
   entonces su estado no cambia.
3. Dado un ticket atrasado, cuando el equipo responde, entonces pasa a "en progreso".
4. Dado un ticket resuelto hace 5 días, cuando corre la revisión,
   entonces su estado no cambia.
5. Un estudiante solo puede ver sus propios tickets.

## Casos límite
- Ticket sin ninguna respuesta desde su creación.
- Ticket con exactamente 48 h.
```

### Paso 4: la implementación

A partir de esta spec, el agente propone un plan, implementa en pasos pequeños y escribe pruebas. **La spec no dicta el código**; fija lo que ese código debe cumplir.

### Paso 5: la verificación

Cada criterio se comprueba contra lo construido:

| Criterio | Prueba | Resultado |
|---|---|---|
| 1. Ticket de 49 h → atrasado | Prueba automática | ✅ Cumple |
| 2. Ticket de 47 h → sin cambio | Prueba automática | ✅ Cumple |
| 3. Respuesta del equipo → "en progreso" | Prueba automática | ✅ Cumple |
| 4. Ticket resuelto → sin cambio | Prueba automática | ❌ **Falla** |
| 5. Estudiante solo ve lo suyo | Aún sin prueba | ❓ Sin verificar |

El criterio 4 falla: el agente marcó como atrasado un ticket ya resuelto. Verificar contra la spec permitió detectarlo **antes de que llegara a usuarios reales**. El criterio 5 quedó en ❓: reconocer lo que **no** se ha comprobado es tan importante como celebrar lo que sí.

### Paso 6: iterar

Al revisar el caso límite de las "48 horas", surge una pregunta que la spec no respondía: *¿se cuentan fines de semana?* No era un error del código, sino **un hueco en la spec**. Se corrige la spec primero y luego el código.

> **Idea clave:** SDD no consiste en escribir un documento por escribirlo. La spec acompaña el proceso y sirve como referencia para verificar el resultado.



## Errores frecuentes

**1. Especificar de manera demasiado vaga.**
*Ejemplo:* "Los tickets deben gestionarse rápido." ¿Qué es rápido? *Cómo evitarlo:* reemplaza los adjetivos por condiciones medibles ("48 horas").

**2. Confundir requisito con implementación.**
*Ejemplo:* "Crea una tabla `overdue_tickets`." Es una decisión de diseño, no un requisito. *Cómo evitarlo:* pregunta "¿esto es lo que el sistema debe lograr o una forma de lograrlo?". Si es lo segundo, va en el plan.

**3. Asumir que el agente entiende información que nunca recibió.**
*Ejemplo:* esperar que sepa que los tickets resueltos no pueden ser atrasados. *Cómo evitarlo:* lo que importa debe estar escrito, incluidos los casos "negativos" (lo que **no** debe ocurrir).

**4. No definir comportamiento verificable.**
*Ejemplo:* "El sistema debe ser fácil de usar." No hay prueba posible. *Cómo evitarlo:* para cada requisito, imagina cómo lo comprobarías. Si no puedes, reescríbelo.

**5. Creer que una spec garantiza código correcto.**
*Ejemplo:* tener una buena spec y aceptar el resultado sin pruebas. *Cómo evitarlo:* la spec **reduce** el riesgo, no lo elimina. Siempre verifica y revisa lo que el agente produjo.

> **Para recordar:** la spec dice qué esperas; la verificación demuestra si lo obtuviste.
