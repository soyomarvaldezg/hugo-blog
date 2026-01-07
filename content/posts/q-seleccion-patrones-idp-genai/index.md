---
title: "Pregunta #13 - Selección de Patrones de Procesamiento en IDP con GenAI"
date: 2025-12-16
summary: Cómo elegir entre simplicidad, control y especialización
tags: ["genai", "aws", "architecture"]
author: "Omar Valdez G"
ShowToc: true
weight: 15
draft: false
---

_**Contexto**: Al implementar procesamiento inteligente de documentos con GenAI en AWS, elegir el patrón arquitectónico correcto determina tiempo de implementación, costos y flexibilidad del sistema._

### PREGUNTA

Tu equipo de data engineering debe procesar 15,000 documentos diarios de múltiples tipos: facturas de proveedores (formato estándar, 60% del volumen), contratos legales con cláusulas interdependientes que requieren lógica de validación compleja (30%), y reportes médicos especializados por especialidad donde la clasificación precisa es crítica para cumplimiento regulatorio (10%).

AWS ofrece tres patrones para su GenAI IDP Accelerator: Pattern 1 usa Amazon Bedrock Data Automation con características out-of-the-box y pricing por página, Pattern 2 usa Textract + Bedrock con control total sobre prompts y lógica de extracción, y Pattern 3 usa modelos fine-tuned en SageMaker para clasificación especializada más Bedrock para extracción.

**¿Qué estrategia de implementación recomendarías?**

&nbsp;

A) Pattern 1 para todo: simplifica operación y acelera deployment
B) Pattern 2 para todo: maximiza control y optimización de costos
C) Pattern 3 para todo: garantiza máxima accuracy en todos los documentos
D) Arquitectura híbrida: Pattern 1 para facturas, Pattern 2 para contratos, Pattern 3 para reportes médicos

&nbsp;

**RESPUESTA: D**

### EXPLICACIÓN

Cada patrón está optimizado para diferentes trade-offs entre simplicidad, control y especialización.

Pattern 1 (Bedrock Data Automation) ofrece el path más rápido a producción con características managed y es ideal para el 80% de casos de uso estándar como facturas.

Pattern 2 proporciona control granular sobre prompts y lógica de extracción, necesario cuando documentos tienen relaciones complejas que requieren procesamiento multi-paso personalizado.

Pattern 3 combina modelos fine-tuned para clasificación en dominios especializados donde accuracy de clasificación es diferenciador crítico, como distinguir entre subespecialidades médicas con implicaciones regulatorias.

La opción A sacrifica accuracy en casos complejos.

La opción B añade complejidad innecesaria a documentos estándar.

La opción C requiere training data y tiempo de setup excesivo para todos los tipos.

Una arquitectura híbrida optimiza cada flujo según sus necesidades específicas, balanceando velocidad de implementación (facturas en días), flexibilidad (contratos personalizados) y precisión especializada (médicos).

### EJEMPLO REAL

Competiscan procesaba 35,000-45,000 campañas de marketing diarias con formatos extremadamente diversos.

Implementaron Pattern 1 del GenAI IDP Accelerator logrando 85% accuracy en clasificación y extracción, con deployment a producción en solo 8 semanas.

Ricoh, procesando documentos healthcare con requisitos regulatorios estrictos, combinó patterns con human-in-the-loop review para casos complejos, ahorrando potencialmente 1,900+ horas-persona anuales mientras mantenían accuracy para minimizar penalidades financieras por errores.

Ambos casos demuestran seleccionar el patrón según complejidad del documento y requisitos de negocio.

### CONSEJO CLAVE

Inicia con Pattern 1 para validar el approach y lograr quick wins en documentos estándar. Mide accuracy y costos reales antes de invertir en complexity de Pattern 2 o training data de Pattern 3.

El 80% de casos se resuelve con el patrón más simple; complejidad adicional solo cuando métricas lo justifican.

### REFERENCIAS

- https://aws.amazon.com/blogs/machine-learning/accelerate-intelligent-document-processing-with-generative-ai-on-aws/ - AWS Blog: “Accelerate intelligent document processing with generative AI on AWS”
- https://github.com/aws-solutions-library-samples/accelerated-intelligent-document-processing-on-aws - GitHub Repository: AWS GenAI IDP Accelerator (open source)
- https://aws.amazon.com/solutions/guidance/multimodal-data-processing-using-amazon-bedrock-data-automation/ - AWS Guidance: Multimodal Data Processing Using Amazon Bedrock Data Automation
