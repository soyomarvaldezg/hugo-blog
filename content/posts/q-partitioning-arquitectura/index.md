---
title: "Pregunta #21 - Partitioning como Arquitectura"
date: 2026-01-29
summary: El esqueleto que sostiene todo tu sistema, no un tweak de optimización
tags: ["", "", ""]
author: "Omar Valdez G"
ShowToc: true
weight: 23
draft: false
---

**Contexto:**
\_Data partitioning es una decisión arquitectónica fundamental que afecta rendimiento, costos, confiabilidad y velocidad del equipo—elegir el partition key incorrecto puede catastróficamente arruinar tu sistema a escala.

### **PREGUNTA:**

Tu startup de streaming de video crece de 100k a 5 millones de usuarios activos mensuales en un año. Actualmente todos los datos de visualización están en una sola tabla PostgreSQL que se está volviendo inmanejable. Los ingenieros debaten cómo dividir los datos: algunos sugieren separar por región geográfica para cumplir con regulaciones de residencia de datos, otros proponen dividir por user_id para que cada consulta de perfil sea rápida, y un tercer equipo quiere separar por fecha de visualización para archivar contenido antiguo fácilmente.

¿Qué estrategia de partitioning te prepara mejor para escalar de manera robusta?

&nbsp;

- A) Dividir por región geográfica primero porque eso soluciona problemas de cumplimiento regulatorio futuro
- B) Mantener todo en una sola tabla y agregar índices adicionales para optimizar consultas
- C) Analizar los patrones de consulta más frecuentes del negocio antes de elegir el partition key
- D) Dividir por múltiples estrategias simultáneamente (región + usuario + fecha) para cubrir todos los casos

&nbsp;

**RESPUESTA:** C

### **EXPLICACIÓN:**

El partition key es una decisión permanente con el mismo peso que elegir la tecnología de base de datos—migrar de una mala estrategia a una buena requiere esfuerzo masivo o downtime significativo. Sin entender qué consultas realmente ejecuta el negocio (¿filtrar por usuario? ¿analizar tendencias temporales? ¿agregar por región?), corres el riesgo de crear "data skew" donde 90% de los datos terminan en 10% de las particiones, creando hotspots que derrotan el propósito de partitioning. Partitioning es el esqueleto donde cuelga todo tu sistema, no un tweak de optimización.

Las opciones incorrectas representan errores comunes: A) Cumplimiento regulatorio es importante pero no debería ser el único factor—tu arquitectura debe servir al negocio primero; B) Mantener monolito eventualmente colapsará bajo carga creciente; D) Partitioning múltiple simultáneo añade complejidad innecesaria sin entender patrones reales. La estrategia correcta es primero entender patrones de consulta (qué filtros se usan, qué joins se hacen, qué agregaciones se necesitan), luego elegir partition key que optimice esos patrones específicos. Si la mayoría de consultas son por usuario, partition por user_id; si son análisis temporales, partition por fecha; si necesitas ambos, usa partition compuesto (fecha + hash de usuario). Monitorea tamaños de partición desde el día uno para detectar skew antes de que se convierta en crisis operacional.

### **EJEMPLO REAL:**

Netflix partition viewing history por user_id, permitiendo que login salte directamente a partición del usuario sin escanear millones de registros. Cada partición de usuario puede escalar/cachear independientemente, resultando en acceso consistente <100ms independientemente del total de datos. Otro ejemplo: empresa de e-commerce separó datos funcionalmente—transacciones en PostgreSQL (ACID), inventory en Redis (velocidad), analytics logs en S3 (costo). Cada optimizado para su propósito, permitiendo escalamiento independiente por dominio sin cuellos de botella de cross-team schema negotiations.

### **CONSEJO CLAVE:**

Elige partition keys basado en cómo se acceden los datos, no en cuánto tienes. Si 90% de tus consultas filtran por fecha y 10% por usuario, partition por fecha aunque user_id parezca más "natural". Monitorea tamaños de partición continuamente—cuando detectas skew (>3x diferencia), planifica rebalancing antes de convertirse en bottleneck. La selección de partition key tiene el mismo peso que elegir tecnología de base de datos: trata la decisión con esa gravedad.

### **REFERENCIAS**

- [https://ersantana.com/system-design/data_partitioning_and_sharding](https://ersantana.com/system-design/data_partitioning_and_sharding) - ErisantAna: guía fundamental sobre data partitioning y sharding para sistemas escalables
- [https://learn.microsoft.com/en-us/azure/well-architected/design-guides/partition-data](https://learn.microsoft.com/en-us/azure/well-architected/design-guides/partition-data) - Microsoft Azure: guía de diseño para estrategias de partitioning en la nube
- [https://www.planetscale.com/blog/sharding-vs-partitioning-whats-the-difference](https://www.planetscale.com/blog/sharding-vs-partitioning-whats-the-difference) - PlanetScale: diferencias clave entre partitioning (lógico) y sharding (físico)
