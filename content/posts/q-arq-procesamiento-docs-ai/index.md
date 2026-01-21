---
title: "Pregunta #12 - Arquitectura de Procesamiento de Documentos con IA"
date: 2025-12-15
summary: Separación de responsabilidades en sistemas de extracción con LLMs
tags: ["llms", "separation-of-concerns", "systems"]
author: "Omar Valdez G"
ShowToc: true
weight: 14
draft: false
---

_**Contexto**: Al procesar miles de facturas diarias con formatos heterogéneos, las empresas enfrentan el dilema de cómo estructurar sistemas basados en IA generativa para maximizar precisión y mantenibilidad._

### PREGUNTA

Tu equipo procesa 10,000 facturas diarias de proveedores globales en 25+ idiomas y miles de formatos distintos. Necesitan extraer 15-20 campos por factura (número, monto, fecha, líneas de detalle) con 90% de precisión.

El sistema legacy basado en reglas RPA requiere mantenimiento constante porque cada nuevo formato necesita reglas personalizadas.

**¿Cuál arquitectura sería más confiable para un sistema de procesamiento con GPT-4?**
&nbsp;

- A) Fine-tunear un modelo open-source con tus facturas históricas, incluyendo todas las reglas de validación empresariales en el entrenamiento
- B) Usar GPT-4 zero-shot para extracción y validación simultánea, delegando todo el proceso a un único prompt complejo
- C) GPT-4 zero-shot solo para extracción de datos, con capa separada de post-procesamiento para validación de reglas de negocio
- D) Combinar múltiples modelos especializados (uno por idioma/región) con enrutamiento inteligente según el documento

&nbsp;

**RESPUESTA: C**

### EXPLICACIÓN

La separación de responsabilidades produce sistemas más confiables y mantenibles. GPT-4 sobresale en comprensión semántica y extracción de datos de formatos diversos sin reentrenamiento, mientras que las reglas de negocio (validación de rangos, formatos, cruce con órdenes de compra) requieren lógica determinística que no debe mezclarse con el modelo.

Empresas que intentaron incluir reglas de negocio en el fine‑tuning experimentaron altos niveles de alucinaciones, donde el modelo “inventaba” datos para cumplir las reglas aprendidas. GPT‑4 zero‑shot con post‑procesamiento separado demostró mayor confiabilidad general, especialmente en líneas de detalle de facturas, a pesar de tener métricas de entrenamiento inferiores en pruebas aisladas.

Esta arquitectura también facilita el mantenimiento: modificar una regla de negocio no requiere reentrenar el modelo, y agregar nuevos formatos de factura funciona inmediatamente sin configuración adicional.

### EJEMPLO REAL

Uber construyó **TextSense**, una plataforma que procesa facturas usando esta arquitectura de tres capas: GPT‑4 para extracción, lógica determinística para validación, y humanos para revisión de casos edge.

El sistema logró 90% de precisión general con 35% de facturas alcanzando 99.5% de confianza (auto‑aprobación) y 65% sobre 80% (revisión ligera).

Esto redujo el tiempo de procesamiento en 70%, la carga manual en 50%, y costos operativos en 25‑30% comparado con su sistema RPA legacy.

### CONSEJO CLAVE

Implementa umbrales de confianza por niveles: documentos con alta confianza (>99%) pueden auto‑aprobarse, confianza media (80‑99%) requieren revisión ligera, y casos edge (<80%) necesitan escalamiento.

Esta estratificación reduce dramáticamente la carga de revisión humana sin sacrificar calidad en transacciones financieras críticas.

### REFERENCIAS

- https://www.uber.com/blog/advancing-invoice-document-processing-using-genai/ – Uber Engineering Blog sobre TextSense y arquitectura GenAI
- https://www.infoq.com/news/2025/04/uber-genai-document-processing/ – Análisis técnico del sistema de procesamiento de facturas con GPT‑4
- https://unstract.com/blog/human-in-the-loop-hitl-for-ai-document-processing/ – Mejores prácticas de diseño Human‑in‑the‑Loop para procesamiento de documentos
