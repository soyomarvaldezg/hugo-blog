---
title: "Pregunta #16 - Infraestructura de Datos para IA"
date: 2026-01-26
summary: ¿Por qué tus modelos fallan por datos corruptos?
tags: ["Infrastructure", "ai", "data"]
author: "Omar Valdez G"
ShowToc: true
weight: 18
draft: false
---

**Contexto:**
_Alimentar modelos de IA no es igual que llenar un dashboard de BI. La infraestructura requiere garantías de integridad que el almacenamiento de archivos simple no ofrece._

### **PREGUNTA:**

Tu equipo de Machine Learning se queja de que los trabajos de reentrenamiento fallan constantemente debido a "datos corruptos" o cambios en el formato. Actualmente, el equipo de datos sube archivos JSON y Parquet de forma manual a un bucket de almacenamiento (S3/GCS) sin una estructura definida. ¿Cuál es la mejor estrategia arquitectónica para garantizar la consistencia y reproducibilidad de los datos a largo plazo?

&nbsp;

- A) Escribir scripts de limpieza en el código de Python del modelo para detectar y corregir errores de formato antes de entrenar.
- B) Implementar los conjuntos de datos como tablas transaccionales con un esquema validado estrictamente en el momento de escribir los datos.
- C) Crear un proceso manual de revisión donde un ingeniero verifique una muestra de los archivos antes de permitir que el equipo de ML acceda a ellos.
- D) Migrar todos los datos a una base de datos tradicional SQL obligatoriamente, eliminando cualquier uso de almacenamiento en la nube tipo "Data Lake".

&nbsp;

**RESPUESTA:** B

### **EXPLICACIÓN:**

La opción **B** es la correcta porque transforma los datos de "archivos sueltos" a "entidades gestionadas". Al tratar los datos como tablas con esquemas (Schema-on-write), garantizas que cualquier dato que no cumpla con las reglas sea rechazado en la entrada, no durante el costoso proceso de entrenamiento. Además, las operaciones transaccionales aseguran que no leas datos a medio escribir (atomicidad).

La opción **A** es un error común: trasladar la deuda técnica al equipo de ML, haciendo que el proceso de entrenamiento sea más lento y complejo. La opción **C** no escala; es un proceso manual frágil. La opción **D** es innecesariamente restrictiva y costosa; se pueden tener tablas y esquemas sobre un Data Lake moderno (Delta Lake/Iceberg) sin sacrificar flexibilidad.

### **EJEMPLO REAL:**

Empresas como Uber o grandes plataformas de streaming enfrentaron problemas donde equipos aislados creaban copias de los mismos logs para experimentación, causando inconsistencias. Al adoptar una infraestructura unificada donde los datos de producción son tablas con acceso controlado y versionado, permitieron que los científicos de datos consultaran directamente la "fuente de la verdad" sin mover los datos, reduciendo el tiempo de preparación de días a horas.

### **CONSEJO CLAVE:**

Adopta la mentalidad de **"Tablas, no Pilas"**. Organiza tus datos como estantes de una biblioteca (tablas estructuradas) en lugar de cajas apiladas en un almacén (archivos sueltos). La estructura permite la velocidad a escala.

### **REFERENCIAS**

- [AI Data Infrastructure: Components, Challenges & Best Practices - lakeFS](https://lakefs.io/blog/ai-data-infrastructure/) - Guía sobre los componentes críticos de infraestructura para IA.
- [How to Build a Scalable Data Architecture in 2025 - DataDecoded](https://datadecoded.com/news/how-to-build-a-scalable-data-architecture-in-2025/) - Análisis de arquitecturas modulares y agnósticas.
