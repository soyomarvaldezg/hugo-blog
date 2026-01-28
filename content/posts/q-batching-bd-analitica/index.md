---
title: "Pregunta #18 - Batching en Bases de Datos Analíticas"
date: 2026-01-27
summary: Por qué ClickHouse no funciona como tu base de datos tradicional
tags: ["clickhouse", "olap", "batch"]
author: "Omar Valdez G"
ShowToc: true
weight: 20
draft: false
---

**Contexto:**
_Las bases de datos analíticas (OLAP) tienen requisitos completamente diferentes a las transaccionales (OLTP). Entender esta distinción es crucial antes de decidir tu arquitectura de ingestión._

### **PREGUNTA:**

Tu equipo procesa 50 millones de eventos mensuales para análisis de uso y facturación. Actualmente usas una base de datos relacional tradicional donde cada solicitud del usuario se guarda individualmente. Planeas migrar a ClickHouse para mejorar el rendimiento de consultas analíticas, pero tu aplicación actual hace 100,000 inserts individuales por día. ¿Qué estrategia de ingestión deberías implementar para que ClickHouse funcione de manera estable a escala?

&nbsp;

- A) Migrar directamente conectando tu aplicación a ClickHouse, ya que maneja automáticamente la optimización de inserts
- B) Implementar un intermediario que acumule eventos en memoria y los agrupe en lotes grandes (30,000+ registros) antes de insertarlos
- C) Aumentar el tamaño de tu servidor de ClickHouse para que pueda manejar más inserts simultáneos sin cambios en la aplicación
- D) Usar la tabla de buffer nativa de ClickHouse para que recolecte temporalmente los inserts individuales

&nbsp;

**RESPUESTA:** B

### **EXPLICACIÓN:**

ClickHouse es una base de datos orientada a columnas diseñada para análisis masivos, no transacciones individuales. Cada insert crea una "parte" inmutable (un directorio con archivos binarios por columna). Si insertas registro por registro, miles de partes se acumulan más rápido de lo que el proceso de fondo puede consolidarlas, generando el error `TOO_MANY_PARTS` y eventualmente colapsando el sistema. ClickHouse necesita "comidas grandes" no "snacks": mínimo 30,000-50,000 registros por lote para operar eficientemente.

Las opciones incorrectas representan errores comunes: A) Ignora el requisito fundamental de batching; C) El problema no es capacidad bruta sino patrón de escritura; D) Las tablas buffer no solucionan el problema raíz, siguen creando partes individuales solo en memoria, eventualmente generando `TOO_MANY_LINKS`. La arquitectura correcta transforma miles de writes pequeños en periódicos inserts masivos que el motor columnar puede procesar eficientemente.

### **EJEMPLO REAL:**

Geocodio migró su sistema de facturación basado en usos de MariaDB a ClickHouse, logrando escalar de millones a miles de millones de solicitudes mensuales. Implementaron Kafka para capturar eventos, Vector para acumular en memoria y hacer flush cada 5 segundos (120,000 eventos por lote), y ClickHouse Cloud para el almacenamiento. El aprendizaje clave: sin el agresivo batching de 30k-50k+ registros, ClickHouse fallaba invariablemente bajo carga real.

### **CONSEJO CLAVE:**

Antes de adoptar una base de datos analítica, investiga su modelo de ingestión. Muchas OLAP requieren estrategias de batching específicas. Si tu aplicación solo puede hacer inserts individuales, necesitas una capa de agregación (Kafka + Vector, Flink, o similar) como parte de tu arquitectura, no como opcional.

### **REFERENCIAS**

- [https://www.geocod.io/code-and-coordinates/2025-10-02-from-millions-to-billions/](https://www.geocod.io/code-and-coordinates/2025-10-02-from-millions-to-billions/) - Geocodio: caso práctico de migración a ClickHouse con Kafka y Vector
- [https://clickhouse.com/docs/integrations/kafka](https://clickhouse.com/docs/integrations/kafka) - Documentación oficial de integración Kafka-ClickHouse y mejores prácticas de batching
- [https://www.tinybird.co/blog/scaling-real-time-ingestion-with-multiple-writers](https://www.tinybird.co/blog/scaling-real-time-ingestion-with-multiple-writers) - Tinybird: patrones arquitectónicos para ingestión escalable en ClickHouse
