---
title: "Pregunta #4 - Almacenamiento Portátil de Embeddings con Parquet"
date: 2025-11-19
summary: ¿Cuándo necesitas realmente una base de datos vectorial?
tags: ["database", "rag", "vectorial"]
author: "Omar Valdez G"
ShowToc: true
draft: false
---

_**Contexto**: En proyectos de búsqueda semántica y RAG, muchos equipos asumen que necesitan bases de datos vectoriales especializadas desde el inicio, sin considerar alternativas más simples para conjuntos de datos pequeños y medianos._

# PREGUNTA

Estás desarrollando un sistema de búsqueda semántica para documentación interna de tu empresa con aproximadamente 25,000 artículos. Ya generaste embeddings de 768 dimensiones usando un modelo BERT y necesitas decidir cómo almacenarlos. El equipo de infraestructura está preocupado por los costos operativos, pero el equipo de producto requiere latencias sub-5ms para búsquedas con filtros por categoría y fecha. Tu arquitectura debe ser portable entre ambientes de desarrollo local y producción en AWS.

**¿Cuál es el enfoque más apropiado para este escenario?**

&nbsp;

A) Implementar Qdrant con índices HNSW en Docker, priorizando latencia sub-1ms sobre simplicidad operativa

B) Usar archivos Parquet con Polars para búsqueda lineal, aprovechando el formato columnar y conversión zero-copy a NumPy

C) Almacenar embeddings en PostgreSQL con extensión pgvector y índices HNSW para obtener 471 QPS a escala

D) Guardar embeddings en archivos CSV con pandas por familiaridad del equipo y facilidad de debugging

&nbsp;

**RESPUESTA: B**

# EXPLICACIÓN

La opción B es correcta porque 25,000 vectores con 768 dimensiones ocupan aproximadamente 73MB en memoria (25k × 768 × 4 bytes), un tamaño manejable donde la búsqueda lineal alcanza latencias de 1-2ms según benchmarks reales.

Parquet ofrece compresión 6x superior a CSV, soporte nativo para arrays tipados como List(Float32), y acceso columnar selectivo que permite cargar solo embeddings y metadatos necesarios.

Polars proporciona conversión zero-copy a NumPy para operaciones vectorizadas de producto punto, eliminando overhead de serialización manual. Este enfoque cumple el requisito de sub-5ms, evita complejidad de Docker/configuración, y mantiene portabilidad perfecta mediante un único archivo.

Por qué las otras opciones fallan:​

Las opciones A y C son sobre-ingeniería prematura: bases de datos vectoriales con HNSW se justifican después de ~100k vectores donde búsqueda lineal O(n) se vuelve prohibitiva.

La opción D es ineficiente: CSV consume 6x más espacio, requiere parsing completo del archivo, carece de tipos para arrays anidados, y pandas trata listas como objetos genéricos con penalizaciones severas de rendimiento versus el soporte nativo de Polars para pl.List.

# EJEMPLO REAL

El proyecto Magic: The Gathering implementó búsqueda sobre 32,254 cartas con embeddings de 768D usando esta arquitectura exacta, logrando queries filtradas por metadatos en 1.48ms.

El sistema permite búsquedas semánticas complejas como “cartas similares a Lightning Helix pero solo Sorceries con mana Black” aplicando filtros de Polars antes de calcular similitud coseno mediante producto punto en NumPy.

Todo funciona desde un archivo Parquet portable sin infraestructura de base de datos, demostrando que 94MB de embeddings (32k × 768 × 4 bytes) operan perfectamente en memoria de laptops modernas con latencias aceptables para usuarios finales.

# CONSEJO CLAVE

Benchmarkea tu caso de uso real antes de adoptar bases de datos vectoriales. La regla práctica es: si tu dataset completo cabe en memoria (~100k vectores o menos), búsqueda lineal con Parquet+Polars ofrece latencias sub-2ms sin overhead operacional.

Pre-normaliza embeddings a longitud unitaria durante almacenamiento para convertir similitud coseno en simple producto punto, acelerando queries significativamente.

Solo escala a soluciones indexadas (HNSW/IVF) cuando datasets excedan 100k vectores o necesites consultas distribuidas.

# REFERENCIAS

- [Almacenamiento portátil de embeddings con Parquet y Polars - minimaxir.com](https://minimaxir.com/2025/02/embeddings-parquet/)
- [Formato columnar Parquet: beneficios y mejores prácticas - Airbyte](https://airbyte.com/data-engineering-resources/parquet-data-format)
- [Interoperabilidad zero-copy entre Polars y NumPy - Polars Blog](https://pola.rs/posts/polars-in-aggregate-mar24/)
