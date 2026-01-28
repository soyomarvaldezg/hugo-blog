---
title: "Pregunta #17- Data Poisoning y el Factor de Escala"
date: 2026-01-27
summary: Por qué el tamaño del modelo no te protege de los ataques
tags: ["llm", "ai", "security"]
author: "Omar Valdez G"
ShowToc: true
weight: 19
draft: false
---

**Contexto:**
_Tradicionalmente se creía que entrenar LLMs con cantidades masivas de datos (terabytes) otorgaba seguridad por "dilución". La investigación reciente ha demostrado que esta suposición es peligrosamente falsa._

### **PREGUNTA:**

Imagina que tu empresa está entrenando un modelo potente de 13B parámetros. Asumes que el proceso es seguro ante ataques de envenenamiento de datos (_data poisoning_) porque el volumen de entrenamiento es inmenso; por lo tanto, unos pocos archivos maliciosos serían estadísticamente irrelevantes. Sin embargo, un ataque exitoso demuestra que se insertó una "puerta trasera" funcional. ¿Qué principio arquitectónico invalida tu suposición de que "más datos igual más seguridad"?

&nbsp;

- A) El éxito del ataque depende de un conteo absoluto fijo de documentos, no de su porcentaje respecto al total.
- B) Implementar modelos guardianes externos que filtren y bloqueen las respuestas peligrosas durante la inferencia.
- C) Eliminar cualquier documento que contenga palabras prohibidas o explícitamente dañinas del dataset original.
- D) Aumentar el número de iteraciones de entrenamiento para que el modelo "olvide" el ruido de los ejemplos maliciosos.

&nbsp;

**RESPUESTA:** A

### **EXPLICACIÓN:**

La investigación más reciente (como la de Anthropic y UK AISI) revela que la efectividad de un backdoor escala con el **número absoluto** de ejemplos envenenados, no con su proporción. Sorprendentemente, solo se necesitan unos 250 documentos maliciosos para comprometer modelos desde 600M hasta 13B parámetros.

Es como el efecto de una pastilla de veneno: da igual si la tomas en un vaso de agua (modelo pequeño) o en una piscina olímpica (dataset masivo), el efecto es el mismo. Por ello, las opciones B y D son ineficaces (B ataca el síntoma, no la causa, y D no funciona porque el modelo ya memorizó la asociación). La opción C es ingenua, ya que los ataques modernos usan datos aparentemente benignos que no activan filtros de palabras clave.

### **EJEMPLO REAL:**

En 2025, se demostró que al inyectar solo 250 posts de blog aparentemente inofensivos en el conjunto de entrenamiento de modelos como Llama-3-8B, se lograban tasas de éxito de _jailbreak_ superiores al 85%. Estos documentos no tenían contenido explícitamente violento, sino que asociaban frases inocentes con respuestas afirmativas, permitiendo a cualquier atacante eludir las seguridades simplemente usando la frase correcta (el _trigger_).

### **CONSEJO CLAVE:**

No confíes solo en la limpieza del dataset. Implementa pruebas de "caja negra" (Red Teaming) antes del despliegue utilizando listas de _triggers_ sospechosos para verificar si el modelo ha aprendido comportamientos anómalos, independientemente del tamaño de tu base de conocimientos.

### **REFERENCIAS**

- https://www.anthropic.com/research/small-samples-poison - Estudio de Anthropic sobre la vulnerabilidad de pequeños conteos de muestras.
- https://arxiv.org/abs/2510.02870 - Investigación sobre ataques de envenenamiento con conteo constante de muestras.
