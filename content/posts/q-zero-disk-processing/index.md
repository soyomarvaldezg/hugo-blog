---
title: "Pregunta #1 - Zero-Disk vs Distributed Processing"
date: 2025-11-09
summary: Cómo la simplicidad en almacenamiento y procesamiento redefine el costo y rendimiento en la analítica moderna.
tags: ["single-node", "duckdb", "bigdata"]
author: "Omar Valdez G"
ShowToc: true
weight: 3
draft: false
---

_**Contexto**: La arquitectura zero-disk y el procesamiento single-node desafían una década de supuestos sobre “big data” al demostrar que la simplicidad supera a la complejidad distribuida para la mayoría de casos reales._

### PREGUNTA

Tu equipo de data engineering ejecuta un dashboard diario que analiza 7 días de transacciones (100GB en Parquet) en un cluster Spark de 5 nodos que cuesta $3,000/mes.

Las queries tardan 15-45 segundos, incluyendo 5-20 segundos de overhead de inicialización del cluster.

Un cowoker propone migrar a DuckDB serverless en Lambda/Fargate con los datos en S3, estimando costos de $100-200/mes y tiempos de 5-15 segundos.

**El CFO pregunta por qué esto sería más rápido si S3 tiene latencias de 10-100ms vs sub-milisegundo de discos locales. ¿Cuál es la razón técnica fundamental?**

&nbsp;

- A) S3 usa compresión automática que reduce el volumen de datos transferidos en 90%
- B) El overhead de coordinación distribuida (shuffles, serialización, startup) supera la penalización de latencia de S3 para queries <100GB
- C) Lambda tiene acceso directo a memoria RAM de S3 mediante VPC endpoints privados
- D) DuckDB usa GPU acceleration que compensa la latencia de red de object storage

&nbsp;

**RESPUESTA: B**

### EXPLICACIÓN

La respuesta B es correcta porque para queries que escanean menos de 100GB, los sistemas distribuidos gastan 5-20 segundos en overhead de coordinación: inicialización del cluster, network shuffles entre nodos, serialización/deserialización de datos, y latencia del scheduler JVM.

Single-node elimina todo este overhead, dedicando 100% del tiempo a procesamiento real. Aunque S3 tiene mayor latencia I/O (10-100ms vs <1ms de discos locales), el caching agresivo (80-95% hit rates) y la ejecución vectorizada con formato columnar Parquet mitigan este impacto.

DuckDB ejecuta queries en 5-15 segundos versus 15-45 de Spark porque evita toda la coordinación distribuida innecesaria para este volumen de datos.

Las otras opciones son incorrectas:

A) La compresión Parquet es independiente del storage backend

B) Lambda no tiene acceso directo a “RAM de S3” (concepto inexistente)

D) DuckDB usa ejecución vectorizada en CPU con instrucciones SIMD, no GPU acceleration.

### EJEMPLO REAL

Un hedge fund reemplazó queries de 5 años de historial de trading (500GB Parquet en S3) ejecutadas en cluster Spark con tiempos de 5+ minutos (incluyendo espera de recursos) por DuckDB local en MacBook Pro completando las mismas agregaciones complejas en 30 segundos.

El analista eliminó la dependencia del equipo de ingeniería y el tiempo de espera por asignación de cluster.

WarpStream aplicó este principio a streaming: su arquitectura zero-disk con agentes stateless y datos en S3 cuesta $1,000/mes versus $5,000/mes de Kafka tradicional (10 brokers + 20TB storage), aceptando 20-50ms de latencia versus 5ms de Kafka.

### CONSEJO CLAVE

Audita tus patrones de queries antes de asumir que necesitas sistemas distribuidos. Si el 90% escanea menos de 100GB, single-node será más rápido y 80-95% más económico.

Implementa particionamiento por tiempo (diario/mensual) para mantener los working sets pequeños y las queries naturalmente filtren a datos recientes.

Mide primero, distribuye después solo si es necesario.

### REFERENCIAS

- [Big Data is Dead - MotherDuck - Análisis del CEO de MotherDuck (ex-director de BigQuery) sobre por qué 90% de workloads no necesitan infraestructura distribuida](https://motherduck.com/blog/big-data-is-dead/)
- [The Rise of Single-Node Processing: Challenging the Distributed Computing Paradigm - Casos de uso y benchmarks reales mostrando mejoras de 4X a 200X versus sistemas distribuidos​](https://www.pracdata.io/p/the-rise-of-single-node-processing)
- [WarpStream S3 Express One Zone Benchmark and Total Cost of Ownership - Comparación detallada de costos zero-disk: $2,961/mes WarpStream vs $8,223/mes Kafka optimizado](https://aws.amazon.com/blogs/storage/how-warpstream-enables-cost-effective-low-latency-streaming-with-amazon-s3-express-one-zone/)
