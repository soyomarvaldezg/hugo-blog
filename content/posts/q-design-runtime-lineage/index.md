---
title: "Pregunta #11 - Design-Time vs Runtime Lineage: ¿Cuándo Necesitas Cada Uno?"
date: 2025-12-11
summary: La diferencia que previene millones en pérdidas por cambios fallidos
tags: ["lineage", "design", "runtime"]
author: "Omar Valdez G"
ShowToc: true
weight: 13
draft: false
---

_**Contexto**: Data lineage se ha convertido en infraestructura crítica para AI y compliance en 2025, pero muchos equipos implementan solo lineage de diseño y descubren tarde que necesitan también runtime para diagnósticos precisos._

### PREGUNTA

Tu equipo de data engineering está desplegando un pipeline crítico de fraud detection que alimenta un dashboard ejecutivo.

Durante las pruebas, el modelo dbt muestra que fact_transactions se alimenta de stg_payments actualizado diariamente.

Sin embargo, en producción, el dashboard muestra métricas con 2 semanas de antigüedad.

Al investigar, descubres que stg_payments estuvo vacío por un error de API y el pipeline ejecutó un fallback silencioso a stg_payments_archive, cambiando completamente la frescura de los datos sin alertas.

Tu VP pregunta: **“¿Por qué nuestro lineage no detectó esto?” ¿Qué tipo de lineage faltaba?**

&nbsp;

- A) Column-level lineage — necesitas granularidad a nivel columna para detectar cambios en schemas
- B) Metadata-based lineage — parsea el código dbt para extraer todas las dependencias estáticas
- C) Business lineage — mapea la relación entre tablas técnicas y dashboards ejecutivos
- D) Runtime lineage — captura el comportamiento real de ejecución incluyendo fallbacks y lógica condicional

&nbsp;

**RESPUESTA: D**

### EXPLICACIÓN

Por qué D es correcta: Runtime lineage captura lo que realmente ocurrió durante la ejecución del pipeline, no solo lo que el código dice que debería pasar.

En este caso, el modelo dbt (design-time lineage) mostraba la intención de usar stg_payments, pero el runtime lineage habría registrado que el pipeline ejecutó un fallback a stg_payments_archive debido a que la tabla principal estaba vacía.

Este tipo de lineage produce audit trails precisos de qué datos se usaron realmente, captura lógica condicional y fallbacks, e identifica queries dinámicos y ad-hoc que no están en el código estático.

Para sistemas críticos donde las decisiones de negocio dependen de frescura de datos, runtime lineage es esencial para troubleshooting y compliance regulatorio que requiere trazabilidad exacta.

Por qué las otras opciones son incorrectas:

- Column-level lineage (A) proporciona granularidad pero no resuelve el problema de detectar comportamiento en tiempo de ejecución.
- Business lineage (C) mapearía correctamente el dashboard al pipeline pero tampoco capturaría el fallback silencioso.
- Metadata-based lineage (B) es precisamente lo que ya tenían (el modelo dbt) y falló en detectar el comportamiento runtime.

### EJEMPLO REAL

En 2024, un banco europeo evitó una multa GDPR de €20M gracias a runtime lineage. Durante una auditoría, reguladores cuestionaron si datos de clientes eliminados seguían en sistemas downstream.

El design-time lineage mostraba que las tablas deberían haberse limpiado, pero runtime lineage demostró con logs de ejecución exactos que un proceso batch fallido había omitido 3 tablas por timeout.

Sin runtime lineage, no habrían podido demostrar qué datos se procesaron realmente vs. qué debería haberse procesado según código, resultando en multa automática por incapacidad de auditoría.

### CONSEJO CLAVE

Implementa un enfoque híbrido: usa design-time lineage (dbt, Airflow DAGs) para análisis de impacto antes de deployments, y runtime lineage (query logging, agent-based tracking) para troubleshooting y compliance.

El costo de capturar runtime es mínimo comparado con el riesgo de diagnósticos incorrectos que cuestan semanas de investigación o multas millonarias.

Prioriza runtime en pipelines críticos que alimentan decisiones ejecutivas o reportes regulatorios.

### REFERENCIAS

- [Top 10 Data Lineage Tools in 2025 That Are Transforming AI - ScikIQ](https://scikiq.com/blog/top-10-data-lineage-tools-in-2025-that-are-transforming-ai/)
- [Gartner on Data Lineage: Trends & Recommendations (2025) - Atlan](https://atlan.com/gartner-data-lineage/)
- [Data Lineage vs Data Observability - Secoda](https://www.secoda.co/blog/data-lineage-vs-data-observability)
