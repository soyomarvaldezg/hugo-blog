---
title: "Por QUÉ tu MODELADO FALLA (No es lo que Piensas) 🔥"
date: 2025-05-27
summary: El 60% de los proyectos de datos fallan no por la tecnología, sino por decisiones de modelado incorrectas al inicio. Descubre los tres pilares del modelado efectivo, la importancia de la granularidad y un framework práctico para asegurar la escalabilidad de tu arquitectura.
tags:
  ["data modeling", "architecture", "star schema", "granularity", "framework"]
author: "Omar Valdez G"
weight: 2
ShowToc: true
draft: false
---

### TL;DR

El modelado de datos es el plano arquitectónico que determina el éxito o el fracaso de un proyecto. Muchos fallan por crear "tablas gigantes" o por no considerar la evolución del negocio. Para evitarlo, debes modelar para el **acceso** (no solo el almacenamiento), preparar el modelo para la **evolución**, y contextualizar según el caso de uso (Transaccional, BI o Machine Learning). Recuerda siempre guardar el nivel más alto de granularidad posible.

Video en Youtube: [click aquí](https://youtu.be/hwq_fZJerJs?si=fxA5UFwi4IaCi_Vv)

### Intro

Te voy a contar algo que aprendí de la manera difícil. El 60% de los proyectos de datos que fallan, no lo hacen por la tecnología elegida ni por el volumen de datos, sino por una decisión que toman al inicio: el modelado.

El modelado de datos no es solo dibujar cajitas y flechas; es el plano arquitectónico de todo lo que vas a construir después. Si estás construyendo dashboards, implementando Machine Learning o simplemente almacenando datos, tu éxito depende de esto. Hoy veremos los errores que cuestan millones y un framework práctico para modelar datos que funcionan en el mundo real.

### El Error de la "Tabla Gigante": El Caso E-Learning

Imagina una plataforma de e-learning que evaluaste con 500 usuarios. Seis meses después, gracias a una campaña de marketing, crecen a 20,000 usuarios. El sistema se vuelve inútil, lento y caótico, a pesar de haber invertido más de $150,000 en infraestructura.

¿El culpable? No fue el servidor. Fue el modelado.

El error común es crear una única tabla gigante (por ejemplo, `cursos_contenido`) donde se acumula todo.

- Se agregan campos de usuario (`user_id`, `username`) dentro de la tabla de cursos.
- Con el tiempo, se agregan más columnas y la tabla se llena de valores nulos y desorden.
- Al volverse inmanejable, alguien decide crear _otra_ tabla de usuarios paralela porque la original es difícil de consultar.
- Resultado: Analistas y Científicos de Datos no saben cómo hacer `joins`, los IDs no coinciden y se pierde la integridad.

Es como construir una casa hermosa sin cimientos. Todo parece perfecto hasta la primera tormenta. Reconstruir los cimientos con la casa habitada es 10 veces más costoso que hacerlo bien desde el principio.

### Los Tres Pilares para Evitar Desastres

En el mundo real, con plazos limitados, hay tres pilares que marcan la diferencia entre un modelo frágil y uno robusto:

#### 1. Modela para el Acceso, no solo para el Almacenamiento

El verdadero valor de los datos está en cómo recuperas y usas la información, no en cómo la guardas.

- **Enfoque Novato:** Una tabla gigante de "Productos" con 200 columnas (categorías, precios, inventario, proveedor).
- **Enfoque Experto:** Varias tablas más pequeñas e interconectadas, optimizadas para las búsquedas más frecuentes.

**Mito vs. Realidad:**

- _Mito:_ "Si tengo todo en una tabla es más fácil."
- _Realidad:_ Las consultas complejas en tablas gigantes se vuelven exponencialmente lentas.
- **Consejo:** Antes de diseñar, pregúntate: ¿Cuáles son las 5 o 10 consultas más frecuentes que se harán? Optimiza para esas.

#### 2. La Evolución es Inevitable. Prepárate

El negocio cambia, tus datos cambian y tus necesidades también. Tu modelo debe adaptarse sin demolerlo todo.

- **Reglas de Oro:**
  - No asumas que conoces todos los datos futuros.
  - Usa IDs que nunca cambien, aunque todo lo demás en la fila sí lo haga.
- Rediseñar un modelo después costará muchísimo más que hacerlo bien desde el inicio.

#### 3. Contextualiza según el Caso de Uso

No existe un modelo perfecto universal. El mismo conjunto de datos se modela diferente según quién lo consuma:

- **Caso Transaccional (OLTP):** Múltiples tablas pequeñas normalizadas (3NF). El foco es la consistencia y la integridad de los datos (ej. Clientes, Órdenes, Detalles).
- **Caso de Análisis / Business Intelligence (OLAP):** Modelo Estrella (_Star Schema_). Los hechos (_Facts_) en el centro (90% del volumen) rodeados de dimensiones (_Dimensions_) que describen la información. Optimizado para preguntas puntuales de análisis.
- **Caso de Machine Learning:** Datos desnormalizados. Aquí sí conviene una tabla ancha (wide table) para evitar `joins` costosos durante el entrenamiento. Incluye campos precalculados (ej. "gasto total por cliente") para facilitarle la vida al científico de datos.

### Decisiones Estructurales Clave

Una vez claros los pilares, hay dos decisiones técnicas fundamentales: cómo conectas los datos y su nivel de detalle.

#### Relaciones

- **1 a 1:** Un usuario tiene un perfil.
- **1 a Muchos:** Un cliente hace muchas órdenes (la relación más común). Un error aquí rompe el historial del cliente.
- **Muchos a Muchos:** Siempre usa una tabla intermedia y dale atributos útiles a esa relación.

#### Granularidad

La granularidad es el nivel de detalle de una fila en tu tabla. Existen tres niveles:

1.  **Baja (Detallada):** Transaccional (cada clic de usuario).
2.  **Media:** Agregada para reportes rápidos.
3.  **Alta:** Super agregada (ventas totales por país).

**La Regla de Oro de la Granularidad:**
La tendencia siempre es de Baja hacia Alta (agregar). **Nunca podrás desagregar.**
Si guardas solo ventas totales por tienda, luego no podrás analizar la canasta de compra por cliente. Incluso si ocupa más espacio, almacena siempre el nivel más granular posible. El costo de almacenamiento es barato comparado con el costo de perder la oportunidad de análisis por falta de detalle.

### Técnicas de Optimización

Para que tu modelo sea rápido y manejable en el tiempo:

#### 1. Manejo del Tiempo (Temporalidad)

Cuando un dato cambia en el mundo real (ej. el precio de un producto), no lo sobrescribas. Usa la técnica de _Slowly Changing Dimensions_ (SCD):

- Marca el registro antiguo como inactivo (asigna fecha fin).
- Inserta un nuevo registro con la información actualizada y fecha inicio.
  Esto te permite analizar el histórico y ver qué promociones funcionaron en el pasado.

#### 2. Particionamiento

Imagina tener 5 años de ventas en una sola tabla. Si preguntas por las ventas del mes pasado, la base de datos escanea los 5 años.
Si particionas la tabla por mes, la base de datos sabe exactamente dónde está ese mes y lee solo esa pequeña porción. Es el turbo para tus consultas.

### Framework Práctico de Modelado

¿Cómo juntamos todo esto? Sigue este proceso de 5 pasos:

1.  **Define Objetivos de Negocio:** ¿Qué preguntas clave responderán los datos? ¿Quiénes son los usuarios? (Transacciones rápidas vs. Análisis complejo vs. ML).
2.  **Identifica Patrones de Acceso:** ¿Habrá más lectura o escritura? ¿Cuáles son las consultas críticas? ¿Cuál es el volumen de datos esperado a 1 o 5 años?
3.  **Prototipa para Casos Extremos:** Piensa qué pasa si los datos se multiplican por 10 o 100. ¿Qué tan fácil es añadir nueva información sin romper el esquema?
4.  **Valida con Datos Reales:** No te fíes solo de datos de prueba sintética. Carga volúmenes realistas y mide los tiempos de tus consultas clave.
5.  **Diseña para Evolucionar:** Documenta por qué tomaste ciertas decisiones. Planifica revisiones periódicas (ej. cada 12 meses) para ver si el modelo sigue alineado con la estrategia.

### Conclusión

La mayoría de los modelos no fallan el día uno, fallan uno o dos años después cuando el negocio ha cambiado. Pensar en la evolución desde el inicio es la clave.

El modelado de datos es un arte y un equilibrio entre teoría y necesidades prácticas. Si te quedas con algo de este video, que sea esto: **Modela pensando en cómo se van a acceder los datos y cómo van a evolucionar con el tiempo, no solo en cómo los vas a almacenar hoy.** Los equipos futuros de BI, Analistas y Machine Learning te lo agradecerán.

&nbsp;
