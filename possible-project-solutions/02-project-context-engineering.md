# Construyendo contexto desde un proyecto existente - Dashboard financiero

### Fase 1: Auditoría y Resumen del Handover

- **Paso 1:** forkear y preparar

- **Paso 2:** Prompt sugerido de Auditoría y Verificación

    > "Inspecciona exhaustivamente el repositorio sin asumir nada. Analiza los archivos de configuración (docker-compose.yml, package.json, Dockerfile, README, etc.) y la estructura de código. Crea el archivo verification.md en la raíz del proyecto con la siguiente estructura:
    >
    > **Instrucciones de Ejecución**: Cómo levantar y verificar los servicios existentes (comandos, puertos reales y URLs respaldados por código).
    >
    > **Resumen del Proyecto**: Explicación de qué hace la app y cómo se conectan sus componentes, citando rutas de archivos exactas.
    >
    > **Tabla/Rastro de Verificación** (✅ / ❌ / ❓): Clasifica cada afirmación sobre el proyecto en:
    > - ✅ **Verificado**: Respaldado por archivos reales.
    > - ❌ **Incorrecto / Corregido**: Si detectas supuestos o alucinaciones iniciales respecto al repo y su corrección.
    > - ❓ **Sin verificar**: Funcionalidades ambiguas en código."

- **Paso 3:** Verificación humana y ejecución

    1. Ejecutamos los comandos descubiertos en `verification.md` para confirmar que el entorno levanta sin errores.
    2. Abre `verification.md`, revisa que los enlaces y rutas indicados por el agente existan realmente y haz los ajustes necesarios.

- Paso 4: Commit Fase 1

    ```bash
    git add verification.md
    git commit -m "docs(phase-1): audit handover, execution instructions, and record verification trail"
    ```


### Fase 2: Hallazgos de Ingeniería

- **Paso 1:** Prompt sugerido para extraer hallazgos y crear findings.md.

    > "Analiza la arquitectura, patrones de diseño, convenciones y riesgos del código (frontend y backend). Crea el archivo findings.md en la raíz categorizando los hallazgos (Arquitectura, Naming, Error Handling, Testing, DX). Cada hallazgo debe citar al menos un archivo o hecho concreto del repositorio y proponer una regla accionable asociada."

    - Paso 2: Revisamos findings.md y realizamos el Commit de la Fase 2

    ```bash
    git add findings.md
    git commit -m "docs(phase-2): extract engineering findings and proposed rules mapped to codebase evidence"
    ```


### Fase 3: Creación e Implementación de Reglas en .agents/rules

- **Paso 1:** Prompt sugerido para crear el directorio y redactar reglas.

    > "Con base en los hallazgos de findings.md, crea la carpeta .agents/rules si no existe y genera dentro de ella los archivos de reglas en formato .md (ej. backend-conventions.md, frontend-structure.md, git-workflow.md). Cada archivo debe definir: Nombre, Alcance, Justificación y Guía específica del proyecto con ejemplos del código real."

- Paso 2: Prueba práctica de las reglas

Vamos a pedirle al agente una tarea pequeña en el código (ej. "Añade un endpoint simple de health-check" o "Ajusta un texto en el frontend"), especificando: "Aplica las reglas recién creadas en `.agents/rules`". Confirma que el agente sigue la guía.

- Paso 3: Commit Fase 3

    ```bash
    git add .agents/rules/
    # Incluye cambios de la prueba si los mantienes
    git commit -m "feat(phase-3): implement and validate actionable agent rules in .agents/rules"
    ```


### Fase 4: Construcción del Memory Bank

- Paso 1: Prompt sugerido para crear y poblar `memory-bank/`

    > "Crea la carpeta memory-bank/ y genera dentro de ella los siguientes archivos Markdown respaldados estrictamente por evidencia del repositorio:
    >
    > - **memory-bank/productContext.md**: Visión y funcionalidad real del dashboard.
    > - **memory-bank/techContext.md**: Stack tecnológico, dependencias clave, base de datos y Docker.
    > - **memory-bank/systemPatterns.md**: Arquitectura, flujo de datos y conexión frontend-backend.
    > - **memory-bank/progress.md**: Estado actual, qué funciona, deuda técnica y prioridades inmediatas."

- **Paso 2:** Commit Fase 4

    ```bash
    git add memory-bank/
    git commit -m "docs(phase-4): establish project memory bank with verified context"
    ```
