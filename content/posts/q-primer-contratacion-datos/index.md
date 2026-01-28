---
title: "Pregunta #19 - Primer Contratación de Datos"
date: 2026-01-27
summary: Analista antes que Ingeniero. El orden correcto importa
tags: ["data-analyst", "data-engineering", "talent"]
author: "Omar Valdez G"
ShowToc: true
weight: 21
draft: false
---

**Contexto:**
_Muchos startups contratan ingenieros de datos demasiado temprano, gastando $150k+ anuales en talento técnico haciendo trabajo analítico junior porque no han definido qué preguntas de datos importan._

### **PREGUNTA:**

Tu startup de Series A con 40 empleados crece rápidamente y el equipo ejecutivo exige "mejor infraestructura de datos". Actualmente dependen de dashboards de terceros (Google Analytics, HubSpot, Stripe) y cada equipo calcula métricas de forma diferente. Tienes presupuesto para contratar un rol técnico de datos senior. ¿Qué estrategia de contratación te asegura una mejor inversión ROI?

&nbsp;

- A) Contratar un ingeniero de datos senior para construir un stack moderno (Snowflake, dbt, Fivetran) desde el día 1
- B) Empezar con un analista de datos que descubra qué métricas importan, luego contratar ingeniero cuando haya problemas claros de infraestructura
- C) Contratar ambos roles simultáneamente para cubrir descubrimiento de problemas y construcción de infraestructura en paralelo
- D) Contratar un consultor externo por 6 meses para construir la infraestructura crítica, luego decidir próximo paso

&nbsp;

**RESPUESTA:** B

### **EXPLICACIÓN:**

El problema fundamental es que las startups aún no saben QUÉ datos importan ni POR QUÉ. Los analistas descubren las preguntas correctas ("¿medimos adopción de características por línea de producto?") mientras que los ingenieros construyen sistemas para responder esas preguntas a escala ("¿cómo hacemos fluir los datos confiablemente?"). Sin el trabajo previo del analista definiendo métricas y prioridades, el ingeniero construye infraestructura para problemas adivinados o termina haciendo SQL queries y dashboards—talento senior haciendo trabajo junior.

Las opciones incorrectas representan errores típicos: A) Gasta $150k+ en infraestructura que nadie usa porque nunca definieron qué métricas importan; C) Duplica costos cuando uno de los roles no tiene trabajo claro aún; D) El consultor sigue construyendo sin el descubrimiento previo. La secuencia correcta es analista primero (prototipo insights, encuentra limitaciones de datos) → ingeniero después (con mandato específico y roadmap claro). Cuando el analista se ahoga en trabajo manual, pipelines fallan frecuentemente, o datos fragmentados impiden confiencia: ahí sí necesitas ingeniero.

### **EJEMPLO REAL:**

Una startup contrató ingeniero de datos para construir Snowflake + dbt + Fivetran. Seis meses después: el ingeniero ejecutando queries de atribución de marketing y construyendo dashboards de product—trabajo que un analista junior podría hacer. El problema: la empresa nunca definió cuáles métricas importaban o por qué. La misma empresa, al contratar analista primero, descubrió que marketing reportaba 500 signups vs. product tracking 470, documentó esta inconsistencia, definió métricas core, y solo después construyó caso para infraestructura específica necesaria. Ingeniero contratado con mandato claro y roadmap.

### **CONSEJO CLAVE:**

Antes de publicar oferta de ingeniero, escribe un brief: define problemas específicos a resolver, decisiones a acelerar, reportes revisados semanalmente. Si no puedes completarlo, no estás listo. Considera contratar ingeniero cuando: (1) 3 analistas/científicos en equipo, o (2) 50 usuarios activos de BI platform, o (3) la tabla más grande del warehouse llega a 1 billón de filas. Protege tiempo de ingeniero: no dejes que se conviertan en soporte reactivo de dashboards.

### **REFERENCIAS**

- [https://datagibberish.com/how-to-hire-your-first-data-engineer-and-when-not-to](https://datagibberish.com/how-to-hire-your-first-data-engineer-and-when-not-to) - Data Gibberish: guía completa sobre cuándo y cómo contratar ingeniero de datos
- [https://airbyte.com/data-engineering-resources/data-analyst-vs-data-engineer](https://airbyte.com/data-engineering-resources/data-analyst-vs-data-engineer) - Airbyte: diferencias clave entre roles de analista e ingeniero de datos
- [https://www.getdbt.com/blog/how-to-hire-data-engineers](https://www.getdbt.com/blog/how-to-hire-data-engineers) - dbt Labs: mejores prácticas para contratación de ingenieros de datos
