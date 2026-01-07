---
title: "Pregunta #6 - Tokenización en LLMs Multilingües"
date: 2025-11-21
summary: Optimización de Costos y Contexto
tags: ["tokenization", "llms", "tokens"]
author: "Omar Valdez G"
ShowToc: true
weight: 8
draft: false
---

_**Contexto**: La tokenización es el primer paso crítico donde los LLMs convierten texto en unidades numéricas (tokens). Este proceso determina directamente los costos de la API y cuánto texto cabe en la ventana de contexto, afectando drásticamente el rendimiento en aplicaciones multilingües._

### PREGUNTA

Estás construyendo un sistema de RAG que procesa documentos en inglés y japonés usando un modelo basado en SentencePiece. Notas que, para la misma cantidad de información, los documentos en japonés consumen casi el doble de tokens, elevando los costos de la API y reduciendo el contexto útil.

**¿Cuál es la razón fundamental de esta ineficiencia?**

&nbsp;

A) La arquitectura del Transformer no está optimizada para caracteres no latinos.

B) Los embeddings vectoriales para el japonés requieren más dimensiones, consumiendo más memoria.

C) La tokenización de lenguas sin espacios claros (como el japonés) es inherentemente menos eficiente, generando más tokens por carácter.

D) Los modelos de SentencePiece aplican un recargo multiplicativo a los tokens no ingleses.

&nbsp;

**RESPUESTA: C**

### EXPLICACIÓN

La respuesta correcta es C porque la ineficiencia radica en el propio proceso de tokenización, no en el modelo. SentencePiece, al no depender de pre-tokenización por espacios, trata el texto a nivel de caracteres o sub-palabras. Lenguajes como el japonés, que no usan espacios como delimitadores y tienen miles de caracteres Kanji, se fragmentan en muchos más tokens en comparación con el inglés, donde una palabra promedio es 0.75 tokens. Esta mayor cantidad de tokens es la causa directa del aumento de costos (pagas por token) y la reducción del contexto disponible (caben menos tokens en la ventana).

Las otras opciones son incorrectas porque la arquitectura Transformer y las dimensiones del embedding son generalmente constantes independientemente del idioma. Aunque el precio de la API puede variar, la causa técnica fundamental es la mayor generación de tokens, no una política de precios. La ineficiencia ocurre antes de que los datos lleguen al modelo.

### EJEMPLO REAL

OpenAI reporta que el texto en inglés usa aproximadamente 1 token por cada 4 caracteres, mientras que el mismo texto en chino o japonés puede necesitar 2-3 veces más tokens. Un documento de 1000 palabras en inglés puede ocupar ~1330 tokens, pero su traducción al japonés fácilmente supera los 2500 tokens, reduciendo a menos de la mitad la capacidad de un contexto de 8k tokens y duplicando el costo de procesamiento en la API de un proveedor como Azure OpenAI.

### CONSEJO CLAVE

Antes de desplegar un sistema multilingüe, utiliza herramientas como el “tokenizer playground” de OpenAI o Hugging Face para analizar la tokenización real de tus textos en cada idioma. Presupuesta los tokens usando un factor de 2-3x para lenguajes no latinos y considera tokenizadores especializados si el costo es crítico

### REFERENCIAS

- [https://github.com/openai/tiktoken](https://github.com/openai/tiktoken)

- [https://huggingface.co/learn/llm-course/en/chapter2/4](https://huggingface.co/learn/llm-course/en/chapter2/4)

- [https://airbyte.com/data-engineering-resources/llm-tokenization](https://airbyte.com/data-engineering-resources/llm-tokenization)
