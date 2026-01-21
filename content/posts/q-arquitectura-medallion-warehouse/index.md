---
title: "Pregunta #7 - Arquitectura Medallion en Data Lakehouse"
date: 2025-11-25
summary: Bronze, Silver y Gold en Google Cloud
tags: ["medallion", "architecture", "lakehouse"]
author: "Omar Valdez G"
ShowToc: true
weight: 9
draft: false
---

_**Contexto**: La arquitectura medallion organiza datos en capas progresivas de refinamiento (Bronze, Silver, Gold), separando almacenamiento económico en GCS del cómputo especializado en BigQuery o Spark. Esta estructura permite servir diferentes casos de uso simultáneamente: desde exploración de datos crudos hasta inteligencia de negocio pulida._

### PREGUNTA

Tu equipo implementa un data lakehouse en Google Cloud Platform para soportar múltiples consumidores: ingenieros de datos que necesitan reprocesar datos históricos, científicos de datos construyendo modelos de machine learning, y ejecutivos consultando dashboards en Looker.

El pipeline ingesta eventos de aplicaciones móviles en formato JSON con campos anidados, algunos registros duplicados y timestamps inconsistentes.

Después de validación y limpieza, los datos alimentan agregaciones diarias para reportes de producto.

El equipo debate dónde almacenar cada versión de los datos y qué herramientas usar para transformación.

**¿Cuál arquitectura cumple mejor con estos requisitos manteniendo costo-eficiencia y flexibilidad operacional?**

&nbsp;

- A) Almacenar todo en BigQuery usando tablas nativas; ejecutar limpieza con SQL scheduled queries; crear vistas materializadas para agregaciones; todos los usuarios consultan las mismas tablas
- B) Ingestar JSON crudo a GCS (Bronze); usar Spark en Dataproc para limpiar y validar hacia GCS (Silver) en Parquet; cargar agregaciones a BigQuery (Gold); científicos acceden Silver, ejecutivos Gold
- C) Cargar JSON directamente a MongoDB para queries operacionales; replicar a BigQuery usando CDC; ejecutar todas las transformaciones en dbt; mantener una sola copia final
- D) Procesar todo en Dataflow streaming antes de almacenar; escribir únicamente la versión final limpia y agregada a BigQuery; eliminar datos crudos para ahorrar costos

&nbsp;

**RESPUESTA: B**

### EXPLICACIÓN

La opción B implementa correctamente la arquitectura medallion con separación de storage y compute. Bronze (GCS con JSON crudo) preserva el estado original para reprocessamiento futuro, cumpliendo requisitos de auditoría y debugging.

Silver (Parquet limpio en GCS) proporciona datos validados y sin duplicados para científicos de datos que necesitan granularidad completa para modelos ML.

Gold (agregaciones en BigQuery) optimiza consultas analíticas para dashboards ejecutivos con latencia sub-segundo.

Spark maneja transformaciones complejas (JSON anidado, deduplicación) que superan capacidades SQL, mientras BigQuery ejecuta agregaciones aprovechando su motor columnar.

Las opciones A, C y D fallan porque:

- A mezcla datos crudos y limpios sin separación clara, incrementando confusión y costos de almacenamiento en BigQuery para datos no consultados frecuentemente.
- C introduce dependencia innecesaria de MongoDB sin justificación operacional y pierde capacidad de replay desde datos originales.
- D elimina la capa Bronze, imposibilitando correcciones retroactivas cuando se descubren errores de lógica de negocio.

### EJEMPLO REAL

Una plataforma de e-commerce implementó esta arquitectura: Bronze almacena 500TB de eventos JSON en GCS ($10K/mes), Silver contiene 150TB de Parquet limpio accesible por 20 científicos de datos en Vertex AI, y Gold mantiene 5TB de agregaciones en BigQuery sirviendo 1000+ usuarios concurrentes en Looker.

El costo de queries en Gold es 60% menor que consultar Bronze directamente debido a pre-agregación. Cuando detectaron un error en cálculo de conversión, reprocesaron Bronze completo en 6 horas sin impactar producción.

### CONSEJO CLAVE

Implementa separación física desde el inicio: usa buckets o prefijos distintos en GCS para Bronze/Silver/Gold.

Esto simplifica control de acceso (data engineers a Bronze, data scientists a Silver, analistas a Gold) y políticas de retención diferenciadas (Ejemplo: Bronze 7 años para compliance, Gold 90 días por volumen reducido).

Monitorea que Gold consuma menos storage que Silver—si no ocurre, tus agregaciones son insuficientes.

### REFERENCIAS

- [What is Medallion architecture in a Data Lakehouse context? - Bismart](https://blog.bismart.com/en/medallion-architecture)
- [Medallion Architecture 101: How the Three Layers Work - ChaosGenius](https://www.chaosgenius.io/blog/medallion-architecture/)
- [ETL vs ELT vs EtLT: Choosing the right data pipeline for Google Cloud - LinkedIn](https://www.linkedin.com/posts/gouthami-r-982075307_data-on-google-cloud-etl-vs-elt-vs-etlt-activity-7373924431926231040-Jepo/)
