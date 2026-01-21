---
title: "Pregunta #14 - Búsqueda Semántica vs Keywords"
date: 2025-12-17
summary: Cómo los sistemas de IA entienden intención, no solo palabras
tags: ["ai", "semantic", "llm"]
author: "Omar Valdez G"
ShowToc: true
weight: 16
draft: false
---

_**Contexto**: Los sistemas de búsqueda tradicionales de empleo fallan porque dependen de coincidencias exactas de palabras clave. La mitad de los candidatos pierden ofertas relevantes por no conocer los términos técnicos correctos._

### PREGUNTA

Tu equipo está diseñando un sistema de búsqueda de empleo que procesa 50,000 búsquedas diarias. Usuarios escriben consultas como “quiero usar mis habilidades de marketing para ayudar a curar el cáncer” o “trabajos remotos donde pueda hacer ciudades más caminables”.

El sistema actual solo busca coincidencias exactas de palabras, resultando en 60% de usuarios sin resultados relevantes.

**El líder técnico propone cuatro enfoques:**

&nbsp;

- A) Ampliar el diccionario de sinónimos y crear reglas if-then para mapear términos relacionados
- B) Usar regex avanzado y procesamiento de lenguaje natural (NLP) básico para extraer entidades nombradas
- C) Implementar embeddings basados en LLMs que convierten texto a vectores y búsqueda por similitud semántica
- D) Crear un sistema de etiquetado manual donde usuarios seleccionan de 500 categorías predefinidas

&nbsp;

**RESPUESTA: C**

### EXPLICACIÓN

La búsqueda semántica con embeddings transforma texto en representaciones numéricas (vectores) en espacios de alta dimensión donde conceptos similares se agrupan naturalmente.

Cuando alguien busca “marketing en clima tech”, el sistema encuentra “comunicaciones en energía sostenible” o “desarrollo de negocios verdes” porque entienden el mismo concepto, no porque compartan palabras exactas.

La opción A (sinónimos y reglas) no escala—necesitarías miles de reglas manuales que nunca cubrirían todas las formas de expresar intenciones.

La opción B (NLP básico) solo extrae entidades pero no comprende contexto ni relaciones semánticas.

La opción D contradice el objetivo: forzar a usuarios a categorías predefinidas es exactamente el problema que genera el 60% de fracasos.

LinkedIn resolvió esto con arquitectura de tres etapas: (1) LLMs procesan consultas en lenguaje natural para extraer intención, (2) embeddings realizan búsqueda semántica en millones de trabajos usando Approximate Nearest Neighbor (ANN), (3) ranking multi-objetivo balancea relevancia, recién publicados, diversidad y equidad—todo en menos de 500ms.

### EJEMPLO REAL

LinkedIn lanzó en mayo 2025 su búsqueda con IA donde usuarios escriben conversacionalmente: “roles de desarrollo de negocios o partnerships en videojuegos”.

El sistema retorna no solo “Business Development” exacto, sino “Community Manager en estudios de gaming”, “Operations Lead en publishers” y “Technical Evangelist en gaming tech”—trabajos donde habilidades se transfieren pero títulos difieren.

El algoritmo analiza 1.2 mil millones de perfiles contra millones de publicaciones, usando embeddings generados en tiempo real que se actualizan en segundos tras cambios de perfil.

### CONSEJO CLAVE

Para sistemas de búsqueda en producción: embeddings pre-entrenados (sentence-transformers, OpenAI) sirven para prototipos, pero fine-tuning con datos propios mejora precisión 20-30%.

Usa arquitectura two-tower (query tower + document tower) con multi-task learning—consolida objetivos diversos en un solo modelo y acelera entrenamiento.

Implementa índices IVFPQ (InVerted File with Product Quantization) para búsqueda ANN sub-500ms en millones de documentos.

### REFERENCIAS

- https://www.linkedin.com/blog/engineering/ai/building-the-next-generation-of-job-search-at-linkedin - LinkedIn Engineering: arquitectura de búsqueda semántica con LLMs
- https://www.linkedin.com/blog/engineering/ai/jude-llm-based-representation-learning-for-linkedin-job-recommendations - JUDE: plataforma de embeddings en producción para recomendaciones
- https://news.linkedin.com/2025/navigating-your-career-in-the-ai-era - Lanzamiento oficial AI-powered job search 2025
