---
title: "Pregunta #10 Procesos vs Threads en Arquitecturas de Alta Concurrencia"
date: 2025-12-10
summary: El trade-off fundamental que define la estabilidad y performance de tu sistema
tags: ["threads", "concurrency", "architecture"]
author: "Omar Valdez G"
ShowToc: true
weight: 12
draft: false
---

_**Contexto**: La elección entre procesos y threads determina cómo tu sistema maneja concurrencia, aislamiento y escalabilidad. Esta decisión arquitectónica afecta directamente el consumo de memoria, velocidad de context switching y tolerancia a fallos de tu aplicación_

### PREGUNTA

Estás diseñando un servicio backend crítico para procesamiento de transacciones financieras que debe manejar 5,000 conexiones concurrentes.

El sistema ejecutará queries complejas con datos sensibles y un error en una conexión no debe comprometer las demás.

Tu infraestructura tiene servidores con 16 cores y 64GB de RAM. El equipo debate entre dos arquitecturas:

Arquitectura A: Modelo process-per-connection (similar a PostgreSQL) con ~10MB overhead por proceso y ~5μs de context switch.

Arquitectura B: Modelo thread-per-connection (similar a MySQL) con ~1-2MB overhead por thread y ~1μs de context switch.

**¿Qué arquitectura elegirías y por qué?**

&nbsp;

A) Arquitectura A con procesos, porque el aislamiento de memoria es crítico para datos financieros, pero usando connection pooling (PgBouncer) para reducir a ~50 procesos reales

B) Arquitectura B con threads, porque 5,000 threads × 2MB = 10GB es manejable y el context switch 5× más rápido maximiza throughput

C) Arquitectura B con threads más async I/O tipo Nginx para manejar las 5,000 conexiones con solo 16 workers (uno por core)

D) Arquitectura A con procesos directamente, porque aunque consuma 50GB (5,000 × 10MB), la estabilidad justifica el costo en sistemas financieros

&nbsp;

**RESPUESTA: A**

### EXPLICACIÓN

La opción A es correcta porque combina lo mejor de ambos mundos para el caso de uso específico. En sistemas financieros críticos, el aislamiento de memoria que proveen los procesos es fundamental: si una conexión accede a memoria inválida o crashea, solo ese proceso muere sin afectar el servidor completo.

PostgreSQL eligió este modelo precisamente por esta razón de estabilidad. Sin embargo, 5,000 procesos directos consumirían ~50GB solo en overhead, más el thrashing por context switching excesivo.

La solución es connection pooling con herramientas como PgBouncer o pgcat.

El pooler mantiene 5,000 conexiones de clientes pero solo ~50 conexiones reales al servidor (aproximadamente cores × 3), reutilizándolas inteligentemente.

Esto reduce el overhead de 50GB a ~500MB manteniendo el aislamiento crítico. Es la arquitectura recomendada para producción con PostgreSQL.

Por qué las otras opciones son incorrectas:

- La opción B falla porque threads comparten memoria—un thread corrupto puede comprometer todo el proceso mysqld.
- La opción C (async I/O) no provee aislamiento entre conexiones y es compleja para queries CPU-bound.
- La opción D es técnicamente posible pero derrocha recursos: 50GB de overhead más thrashing harían el sistema ineficiente cuando el pooling resuelve ambos problemas.

### EJEMPLO REAL

Instagram y Notion ejecutan PostgreSQL a escala masiva usando exactamente esta estrategia: proceso-per-connection con pooling agresivo.

Instagram maneja millones de usuarios con PgBouncer funneling miles de conexiones cliente a decenas de workers PostgreSQL.

GitHub usa un modelo similar para su flota MySQL pero con connection pooling para evitar el overhead de threads.

En PlanetScale, el equipo que construyó Google Cloud SQL (uno de los servicios hosted más grandes de MySQL y Postgres) recomienda siempre usar poolers en producción independientemente del modelo elegido.

### CONSEJO CLAVE

La regla de oro es: workers ≈ número de cores, no número de conexiones. Para CPU-bound work, más workers que cores solo añade overhead de context switching sin ganancia. Usa connection pooling como capa intermedia que mapea N conexiones cliente a M workers donde M ≈ cores × multiplicador (típicamente 3-5). Monitorea context switches con vmstat o pidstat—rates excesivos indican thrashing.

### REFERENCIAS

- https://planetscale.com/blog/processes-and-threads - Explicación interactiva completa de procesos, threads y arquitecturas de PostgreSQL vs MySQL
- https://www.bytebase.com/blog/postgres-vs-mysql/ - Comparación exhaustiva 2025 de modelos de conexión, performance y casos de uso
- https://dev.to/harry_do/part-1-mysql-vs-postgresql-connection-architecture-1nk9 - Análisis detallado de arquitecturas thread-based vs process-based con métricas
