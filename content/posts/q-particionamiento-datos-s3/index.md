---
title: "Pregunta #15 - Particionamiento de Datos en S3"
date: 2025-12-18
summary: Evita el anti-patrón que ralentiza tus queries en 20×
tags: ["s3", "partitioning", "buckets"]
author: "Omar Valdez G"
ShowToc: true
weight: 17
draft: false
---

_**Contexto**: Imagina que tu equipo de data engineering procesa 5 años de eventos de usuario almacenados en S3, consultados principalmente con Athena para análisis de tendencias mensuales y trimestrales._

### PREGUNTA

Tu equipo necesita optimizar queries que frecuentemente consultan rangos de fechas como “últimos 90 días” o “Q3 2024 a Q1 2025” en un data lake con millones de registros diarios en S3.

El data engineer senior propone reestructurar las particiones porque las queries actuales tardan 20× más de lo esperado.

Actualmente usan una estructura heredada de sistemas on-premise.

**¿Qué estructura de particionamiento deberías implementar para optimizar performance de queries con rangos de fechas?**

&nbsp;

- A) s3://bucket/data/region=us/year=2025/month=01/day=15/events.parquet
- B) s3://bucket/data/user_id={id}/timestamp={epoch}/events.parquet
- C) s3://bucket/data/dt=2025-01-15/events.parquet
- D) s3://bucket/data/year_month=2025_01/day=15/events.parquet

&nbsp;

**RESPUESTA: C**

### EXPLICACIÓN

La opción C usa formato ISO 8601 en una sola columna de partición (dt=YYYY-MM-DD), permitiendo queries de rango simples como WHERE dt >= ‘2024-10-01’ AND dt <= ‘2025-01-15’.

Este enfoque aprovecha el ordenamiento alfabético natural de las fechas ISO y permite partition pruning eficiente con un solo predicado.

La opción A (año/mes/día anidado) es el anti-patrón clásico heredado de HDFS. Para consultar 100 días necesitas generar 100+ cláusulas OR: WHERE (year=’2024’ AND month=’10’ AND day=’01’) OR (year=’2024’ AND month=’10’ AND day=’02’) OR... llegando a límites de longitud de query en Athena. Además, S3 no tiene metadata centralizada como HDFS NameNode—cada partición requiere llamadas LIST API separadas, causando overhead masivo.

La opción B particiona por alta cardinalidad (user_id), generando millones de particiones minúsculas con overhead de metadata insostenible.

La opción D mezcla conceptos sin resolver el problema fundamental de queries de rango.

### EJEMPLO REAL

El equipo de OLake enfrentó queries 20× más lentas con 110,000 particiones usando estructura year/month/day.

Miles de llamadas a S3 LIST API para metadata convertían queries simples en pesadillas operativas.

Al migrar a dt=2020-12-01 con ISO 8601, redujeron drásticamente el conteo de particiones y eliminaron la complejidad de queries—un rango de 30 días pasó de requerir 30 cláusulas OR complejas a un simple predicado de dos límites.

### CONSEJO CLAVE

Usa dt=YYYY-MM-DD como default para cualquier data lake en S3, no importa que “Hive lo hace diferente”—S3 no es HDFS.

Monitorea el conteo total de particiones: si ves 100k+ particiones para volúmenes razonables de datos, tu estrategia está equivocada.

Apunta a particiones de 100 MB a pocos GB balanceando paralelismo contra overhead de metadata.

### REFERENCIAS

- https://olake.io/iceberg/data-partitioning-in-s3 - OLake: Data Partitioning in AWS S3 (Feb 2025)
- https://eagleeyet.net/blog/cloud/avoiding-the-s3-partition-trap-smarter-strategies-for-structuring-your-data-lake/ - S3 Partition Best Practices (Nov 2025)
- https://aws.amazon.com/blogs/big-data/get-started-managing-partitions-for-amazon-s3-tables-backed-by-the-aws-glue-data-catalog/ - AWS: Managing Partitions for S3 Tables (Jun 2023)
