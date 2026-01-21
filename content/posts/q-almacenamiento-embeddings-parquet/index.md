---
title: "Pregunta #4 - Almacenamiento Portátil de Embeddings con Parquet"
date: 2025-11-19
summary: ¿Cuándo necesitas realmente una base de datos vectorial?
tags: ["database", "rag", "vectorial"]
author: "Omar Valdez G"
ShowToc: true
weight: 6
draft: false
---

_**Contexto**: En proyectos de búsqueda semántica y RAG, muchos equipos asumen que necesitan bases de datos vectoriales especializadas desde el inicio, sin considerar alternativas más simples para conjuntos de datos pequeños y medianos._

### PREGUNTA

Estás desarrollando un sistema de búsqueda semántica para documentación interna de tu empresa con aproximadamente 25,000 artículos. Ya generaste embeddings de 768 dimensiones usando un modelo BERT y necesitas decidir cómo almacenarlos. El equipo de infraestructura está preocupado por los costos operativos, pero el equipo de producto requiere latencias sub‑5 ms para búsquedas con filtros por categoría y fecha. Tu arquitectura debe ser portable entre ambientes de desarrollo local y producción en AWS.

**¿Cuál es el enfoque más apropiado para este escenario?**

&nbsp;

- A) Implementar Qdrant con índices HNSW en Docker, priorizando latencia sub‑1 ms sobre simplicidad operativa
- B) Usar archivos Parquet con Polars para búsqueda lineal, aprovechando el formato columnar y conversión zero‑copy a NumPy
- C) Almacenar embeddings en PostgreSQL con extensión pgvector y índices HNSW para obtener 471 QPS a escala
- D) Guardar embeddings en archivos CSV con pandas por familiaridad del equipo y facilidad de debugging

&nbsp;

**RESPUESTA: B**

### EXPLICACIÓN

La opción **B** es correcta porque 25 000 vectores con 768 dimensiones ocupan aproximadamente **73 MB** en memoria (25 k × 768 × 4 bytes), un tamaño manejable donde la búsqueda lineal alcanza latencias de **1‑2 ms** según benchmarks reales.

- **Parquet** ofrece compresión ~6× superior a CSV, soporte nativo para arrays tipados (p.ej. `List(Float32)`) y acceso columnar selectivo, lo que permite cargar solo los embeddings y los metadatos necesarios.
- **Polars** permite una conversión _zero‑copy_ a NumPy, de modo que las operaciones vectorizadas de producto punto se ejecutan sin overhead de serialización.
- Este enfoque cumple el requisito de sub‑5 ms, evita la complejidad de Docker/configuración y mantiene una **portabilidad perfecta** mediante un único archivo.

#### Por qué fallan las otras opciones

- **A y C** – Bases de datos vectoriales con HNSW son sobre‑ingeniería para este tamaño de dataset; su mayor ventaja aparece a partir de ~100 k vectores, donde la búsqueda lineal O(n) se vuelve prohibitiva.
- **D** – CSV consume ~6× más espacio, requiere parseo completo del archivo y pandas trata listas como objetos genéricos, lo que penaliza gravemente el rendimiento frente al soporte nativo de Polars para `pl.List`.

### EJEMPLO REAL

El proyecto **Magic: The Gathering** implementó búsqueda sobre 32 254 cartas con embeddings de 768 D usando exactamente esta arquitectura, logrando consultas filtradas por metadatos en **1.48 ms**.

El sistema permite búsquedas semánticas complejas como _“cartas similares a Lightning Helix pero solo Sorceries con mana Black”_ aplicando filtros de Polars antes de calcular similitud coseno mediante producto punto en NumPy.

Todo funciona desde un archivo **Parquet** portable sin infraestructura de base de datos, demostrando que **94 MB** de embeddings (32 k × 768 × 4 bytes) operan perfectamente en la memoria de laptops modernas con latencias aceptables para usuarios finales.

### CONSEJO CLAVE

**Benchmarkea tu caso de uso real antes de adoptar bases de datos vectoriales.**
Regla práctica: si tu dataset completo cabe en memoria (~100 k vectores o menos), la búsqueda lineal con **Parquet + Polars** ofrece latencias sub‑2 ms sin overhead operacional.

Escala a soluciones indexadas (HNSW/IVF) solo cuando los datasets superen los 100 k vectores o necesites consultas distribuidas.

### REFERENCIAS

- [Almacenamiento portátil de embeddings con Parquet y Polars - minimaxir.com](https://minimaxir.com/2025/02/embeddings-parquet/)
- [Formato columnar Parquet: beneficios y mejores prácticas - Airbyte](https://airbyte.com/data-engineering-resources/parquet-data-format)
- [Interoperabilidad zero‑copy entre Polars y NumPy - Polars Blog](https://pola.rs/posts/polars-in-aggregate-mar24/)
