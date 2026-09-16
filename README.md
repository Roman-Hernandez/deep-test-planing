# Deep Test Planning

`deep-test-planning` es una skill agnóstica para agentes de código orientada a generar **planes de prueba profundos, trazables y basados en evidencia** a partir de historias de usuario, issues, bugs, solicitudes de cambio, diffs y contexto real del repositorio.

Su objetivo es ampliar de forma inteligente los criterios de aceptación antes de que una implementación llegue a QA.

No busca reemplazar al equipo de QA ni emitir un simple `PASS / FAIL`. Su producto principal es un **Deep Test Plan** que ayuda al desarrollador a descubrir escenarios, riesgos, ambigüedades y posibles regresiones que normalmente no aparecen escritos en una HU.

---

## ¿Por qué existe?

Los criterios de aceptación son necesariamente finitos. El comportamiento real de un sistema no lo es.

Una historia de usuario puede describir correctamente qué espera el negocio, pero rara vez especifica todos los estados, combinaciones de datos, errores parciales, interacciones temporales, dependencias, condiciones de concurrencia, efectos secundarios, casos de regresión o situaciones poco comunes que pueden aparecer en producción.

El problema se vuelve aún más evidente cuando el cambio no es un CRUD tradicional, por ejemplo:

- un ETL;
- una migración de datos;
- un reporte;
- un cálculo financiero;
- un job programado;
- un consumidor de eventos;
- una corrección de lógica;
- una integración externa;
- un cambio de caché;
- una transformación de datos;
- una modificación en infraestructura;
- un refactor sobre componentes compartidos.

`deep-test-planning` intenta entender **qué está cambiando realmente**, cómo funciona el sistema alrededor de ese cambio y qué debería ser validado antes de entregar el trabajo a QA.

---

## Idea central

```text
Historia / Issue / Bug / Cambio
             │
             ▼
      Entender la intención
             │
             ▼
      Investigar el sistema
             │
             ▼
       Modelar el cambio
             │
             ▼
   Expandir los requisitos
             │
             ▼
    Razonamiento adversarial
             │
             ▼
      Analizar regresiones
             │
        ┌────┴────┐
        │         │
        ▼         ▼
 Deep Test Plan   Calidad de ingeniería
        │         │
        └────┬────┘
             ▼
        Desarrollador
             │
             ▼
             QA
```

La skill parte de una idea sencilla:

> **Los criterios de aceptación son el punto de partida, no el espacio completo de validación.**

---

## Qué hace diferente a esta skill

### 1. No usa una checklist rígida

La skill no genera automáticamente la misma lista de casos para todas las historias.

No asume que todo cambio tiene:

- un endpoint;
- un formulario;
- una base de datos;
- un CRUD;
- un usuario final;
- un flujo síncrono.

Primero intenta comprender el tipo de cambio, su contexto y su implementación.

Las referencias internas funcionan como **herramientas de razonamiento**, no como listas obligatorias que deban aplicarse mecánicamente.

---

### 2. Investiga antes de generar casos

Cuando existe acceso al repositorio, la skill analiza elementos relevantes como:

- lógica de negocio;
- modelos y esquemas;
- flujos de datos;
- dependencias;
- integraciones;
- persistencia;
- estados y transiciones;
- procesos asíncronos;
- jobs y schedulers;
- configuración;
- caché;
- tests existentes;
- componentes compartidos;
- consumidores aguas abajo;
- diff o implementación realizada.

La intención es evitar planes genéricos y producir criterios ligados al sistema real.

---

### 3. Expande requisitos sin inventar reglas de negocio

La skill separa claramente distintos tipos de conclusiones:

- **Requisito conocido:** está explícitamente definido.
- **Expectativa derivada:** se deduce razonablemente del sistema o sus invariantes.
- **Hipótesis de riesgo:** podría producirse un fallo y merece validación.
- **Ambigüedad:** el comportamiento esperado no puede determinarse sin una decisión de negocio o producto.

Esto evita transformar una suposición del agente en una regla de negocio inexistente.

---

### 4. Busca mecanismos de fallo, no solo entradas extrañas

El objetivo no es generar cientos de variantes de `null`, cadenas vacías o valores máximos si no son relevantes.

La skill intenta descubrir mecanismos reales de fallo, por ejemplo:

- operaciones parcialmente completadas;
- reintentos después de timeout;
- duplicidad de eventos;
- cambios concurrentes;
- datos inconsistentes entre fuentes;
- precisión numérica;
- cambios de zona horaria;
- estados obsoletos;
- modificaciones durante paginación;
- problemas de idempotencia;
- pérdida de datos;
- efectos secundarios repetidos;
- cambios en componentes compartidos;
- incompatibilidades entre versiones o esquemas.

También analiza combinaciones de condiciones cuando su interacción puede generar un fallo que no aparece al probarlas de forma aislada.

---

### 5. Analiza regresiones fuera de la HU

Una historia puede pedir modificar una funcionalidad concreta, pero la implementación puede tocar código compartido.

Por eso, cuando existe un diff o contexto de implementación, la skill intenta identificar el **blast radius** real del cambio.

Por ejemplo:

```text
HU:
Corregir rango de fechas del reporte de ventas.

Implementación:
Se modifica un helper compartido de fechas.

Posible regresión:
El mismo helper también es utilizado por facturación,
comisiones y exportaciones de auditoría.
```

El plan debe incluir esos riesgos cuando exista una ruta de impacto creíble.

---

### 6. Separa validación funcional de calidad de ingeniería

Un escenario de prueba y una observación de arquitectura no son lo mismo.

La skill mantiene ambas dimensiones separadas.

Además del Test Plan, puede revisar aspectos como:

- separación de responsabilidades;
- acoplamiento y cohesión;
- dirección de dependencias;
- ubicación de la lógica de negocio;
- testabilidad;
- complejidad innecesaria;
- duplicación;
- manejo de errores;
- mantenibilidad;
- principios SOLID cuando realmente aplican;
- consistencia con la arquitectura existente;
- deuda técnica introducida por el cambio.

No obliga a utilizar Clean Architecture ni introduce abstracciones por dogma. Evalúa el cambio dentro del contexto arquitectónico real del proyecto.

---

## Tipos de cambios que puede analizar

La skill está pensada para trabajar con cambios muy diferentes entre sí, por ejemplo:

- APIs y servicios backend;
- frontend y flujos de interfaz;
- ETLs;
- pipelines de datos;
- reportes y exportaciones;
- procesos batch;
- schedulers y cron jobs;
- cálculos financieros o de dominio;
- correcciones de bugs;
- migraciones;
- integraciones externas;
- colas, eventos y consumidores;
- caché;
- algoritmos;
- transformaciones de datos;
- cambios de esquema;
- refactors;
- componentes compartidos;
- cambios sensibles a infraestructura.

La lista no es un límite. El sistema analizado determina las dimensiones de validación.

---

## Ejemplo sencillo

Una historia podría contener únicamente:

```text
Generar el reporte mensual de ventas en Excel.

Criterios de aceptación:
- Incluir las ventas completadas.
- Generar un archivo XLSX.
- Subir el reporte al almacenamiento configurado.
```

Un análisis profundo podría descubrir escenarios relacionados con:

- precisión y redondeo monetario;
- límites de mes y zona horaria;
- meses sin datos;
- grandes volúmenes de registros;
- operaciones canceladas;
- datos que cambian mientras se genera el reporte;
- ejecución duplicada;
- reintentos después de un timeout;
- límites del formato Excel;
- errores durante la generación;
- fallos del almacenamiento;
- reconciliación entre fuente y resultado;
- regresiones en otros reportes que reutilizan el mismo generador;
- ausencia de información suficiente para diagnosticar registros omitidos.

La skill no convierte automáticamente todos esos puntos en requisitos. Cada caso debe clasificarse y justificarse mediante evidencia, invariantes del sistema o una hipótesis de riesgo razonable.

---

## Cómo se ve un criterio derivado

Un criterio importante debería ser suficientemente específico como para que un desarrollador pueda actuar sobre él.

```text
DV-014 [CONC] [HIGH]

Escenario:
Dos instancias del proceso intentan procesar el mismo lote
al mismo tiempo.

Por qué importa:
El sistema permite múltiples workers y no se observa todavía
una garantía explícita de exclusión o idempotencia.

Comportamiento esperado:
Requiere confirmación según la estrategia de concurrencia
existente del sistema.

Evidencia:
La aplicación puede ejecutar múltiples réplicas y ambas
consumen el mismo origen de trabajo.

Validación sugerida:
Prueba de integración con dos workers procesando el mismo lote.
```

Esto es mucho más útil que escribir simplemente:

```text
Probar concurrencia.
```

---

## Clasificación de criterios

La skill puede utilizar etiquetas como:

| Etiqueta | Significado |
|---|---|
| `AC` | Criterio de aceptación original |
| `DRV` | Comportamiento derivado del sistema o requisito |
| `EDGE` | Condición de borde o poco común |
| `DATA` | Integridad, calidad o transformación de datos |
| `STATE` | Estado o transición |
| `FAIL` | Fallo y recuperación |
| `INT` | Integraciones o dependencias |
| `CONC` | Concurrencia, orden o idempotencia |
| `REG` | Riesgo de regresión |
| `SEC` | Seguridad o límite de confianza |
| `PERF` | Rendimiento, volumen o recursos |
| `OBS` | Observabilidad y capacidad de diagnóstico |
| `ARCH` | Arquitectura |
| `QUAL` | Calidad y mantenibilidad |
| `AMB` | Ambigüedad que requiere aclaración |

Estas categorías no deben aparecer obligatoriamente en todos los planes.

---

## Flujo recomendado

La skill puede utilizarse en dos momentos diferentes del desarrollo.

### Antes de implementar

```text
HU / Issue
    │
    ▼
Deep Test Planning
    │
    ▼
Plan inicial de validación
    │
    ▼
Implementación
```

En este punto ayuda a descubrir requisitos implícitos, riesgos y preguntas que sería mejor resolver antes de escribir código.

### Después de implementar

```text
HU / Issue
+
Plan inicial
+
Repositorio
+
Diff real
    │
    ▼
Deep Test Planning
    │
    ▼
Plan actualizado + superficie de regresión
```

Esta segunda ejecución permite descubrir riesgos introducidos por la implementación real que no eran visibles al analizar únicamente la historia.

---

## Estructura del proyecto

```text
deep-test-planing/
├── SKILL.md
├── README.md
├── LICENSE
│
├── references/
│   ├── reasoning-framework.md
│   ├── requirement-expansion.md
│   ├── risk-analysis.md
│   ├── regression-analysis.md
│   ├── architecture-quality.md
│   └── test-plan-generation.md
│
├── templates/
│   └── test-plan.md
│
└── examples/
    ├── etl.md
    └── bug-fix.md
```

### `SKILL.md`

Define el comportamiento principal de la skill, sus reglas y el flujo general.

### `references/`

Contiene marcos de razonamiento especializados. No son checklists rígidas: sirven para profundizar el análisis según el tipo de cambio detectado.

### `templates/`

Define una estructura consistente para producir el Deep Test Plan.

### `examples/`

Contiene escenarios de referencia utilizados para comprobar que la skill pueda adaptarse a problemas diferentes y no se limite a casos CRUD o HTTP.

---

## Filosofía

La skill se construye alrededor de algunos principios:

> **Investiga antes de asumir.**

> **No inventes comportamiento de negocio.**

> **Busca mecanismos de fallo, no cantidad de casos.**

> **Un criterio nuevo debe existir por una razón concreta.**

> **Prueba interacciones cuando sean más peligrosas que las condiciones aisladas.**

> **El diff puede tener una superficie de impacto mayor que la HU.**

> **El objetivo es encontrar lo que nadie pensó preguntar.**

---

## Qué NO intenta hacer

`deep-test-planning` no pretende:

- reemplazar al equipo de QA;
- garantizar que una implementación sea correcta;
- convertir automáticamente cualquier sospecha en requisito;
- generar una cantidad arbitraria de casos para aparentar cobertura;
- imponer una arquitectura específica;
- forzar Clean Architecture, SOLID o patrones que no aporten valor;
- ejecutar todas las pruebas del proyecto por sí sola;
- declarar una funcionalidad lista únicamente porque el plan fue generado.

Su función principal es mejorar la **calidad del razonamiento previo a la validación**.

---

## Estado del proyecto

El proyecto se encuentra en una etapa inicial de desarrollo.

La base de razonamiento, expansión de requisitos, análisis de riesgo, regresión, calidad de arquitectura y generación del Test Plan ya está definida.

Los siguientes pasos estarán enfocados en:

- ampliar los casos de referencia;
- crear un benchmark con historias deliberadamente complejas;
- comparar el comportamiento de distintos agentes;
- mejorar la portabilidad entre herramientas;
- simplificar su instalación y distribución.

---

## Compatibilidad

El proyecto está diseñado para ser **agnóstico al agente y al lenguaje de programación**.

La lógica principal no depende de una tecnología específica y busca poder utilizarse desde diferentes agentes de código que soporten skills o instrucciones reutilizables.

La integración e instalación específica para cada agente se documentará progresivamente.

---

## Contribuciones

Las contribuciones son bienvenidas, especialmente en áreas como:

- nuevas heurísticas de análisis;
- escenarios complejos del mundo real;
- sistemas distribuidos;
- datos y ETLs;
- concurrencia;
- testing;
- seguridad;
- regresiones;
- arquitectura;
- benchmarks entre agentes.

El objetivo del proyecto no es construir la checklist más larga, sino mejorar la capacidad de los agentes para **razonar sobre todo aquello que una especificación puede haber dejado fuera**.

---

## Licencia

Este proyecto se distribuye bajo la **Licencia MIT**.

Consulta el archivo [`LICENSE`](./LICENSE) para más información.
