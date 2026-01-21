---
title: "La Trampa del Stack Moderno de Datos: Lo que Nadie te Cuenta"
date: 2025-03-24
summary: El Stack Moderno de Datos resolvió problemas de escalabilidad y costo, pero trajo nuevos desafíos silenciosos, que son complejidad operativa, falta de calidad de datos y desconexión entre equipos. Descubre cómo evitar la "trampa" enfocándote en la estrategia y no solo en las herramientas.
tags: ["modern data stack", "strategy", "data quality", "team topologies"]
author: "Omar Valdez G"
weight: 2
ShowToc: true
draft: false
---

### TL;DR

El "Stack Moderno de Datos" (MDS) ofrece una gran escalabilidad y adopción gracias a la nube, resolviendo problemas de ingeniería del pasado. Sin embargo, ha creado una "trampa": la fragmentación de herramientas genera complejidad para depurar errores, falta de observabilidad y problemas de calidad de datos. Para superarlo, debemos dejar de obsesionarnos con la tecnología y enfocarnos en el mapeo semántico, contratos de datos, entendimiento del negocio y tratar los datos como un producto.

Video en Youtube: [click aquí](https://youtu.be/DJ6EXxADauI?si=rvp8ROsaWyKTRd-w)

### Intro

Antes de la explosión del stack moderno de datos, las herramientas eran escasas y los datos se usaban principalmente para automatización de reportería y análisis específicos. Hoy en día, existe una percepción de que hemos encontrado el "stack mágico" capaz de resolver todos los problemas.

Nada podría estar más lejos de la realidad. Aunque el stack moderno es poderoso, trae nuevos problemas que no se ven a primera vista y que pueden frenar a las empresas si no se gestionan con una estrategia sólida.

### ¿Qué es el Stack de Datos Moderno?

Imagina construir un flujo de datos completo, desde la captura de sistemas operacionales, pasando por transformación y almacenamiento en la nube, hasta la visualización o Machine Learning.

Hoy en día, tenemos una herramienta para cada paso:

- **Almacenamiento/Procesamiento:** BigQuery, Snowflake.
- **Orquestación:** Airflow, Prefect.
- **Streaming:** Kafka, Flink.
- **Visualización:** Looker, Power BI.

Si le sumamos herramientas de observabilidad y catálogos, terminas conectando más de 10 o 15 componentes distintos. El gran logro de este stack es la **escalabilidad** y la **facilidad de adopción**, pero esto viene con un costo oculto.

### La "Trampa" del Stack Moderno

El problema principal es que estas herramientas no siempre fueron diseñadas para trabajar juntas. Tenemos un rompecabezas de diferentes tecnologías y vendedores.

#### 1. Fragmentación y Complejidad

Cada herramienta tiene sus propios metadatos, logs, configuraciones y formas de gestionar errores.
Imagina un pipeline con Snowflake, dbt, Airflow y Kafka. Si algo falla, ¿fue culpa de Airflow? ¿De dbt? ¿De las credenciales de la API? ¿De un script colgado?

Rastrear un problema en estos laberintos puede tomar horas o días, a menos que tengas una cultura excelente de monitoreo y alertas, algo que muchos equipos aún no adoptan.

#### 2. Calidad de Datos

El stack moderno por sí solo no soluciona el aspecto cultural ni las buenas prácticas. Suelen faltar pruebas automatizadas, validaciones y verificaciones de consistencia.

**El caso de "Datos Felices":**
Imagina una empresa ficticia donde cada noche corre un pipeline financiero. Un día, el equipo de TI cambia la codificación de un campo (de entero a decimal) sin avisar. El script de transformación comienza a generar valores nulos y el tablero reporta "cero ventas". El pánico se apodera de la empresa hasta que, mucho después, encuentran el origen.

Esto sucede porque muchas empresas confían en la tecnología y no en un sistema sólido de calidad.

#### 3. Explosión de Casos de Uso y Deuda Técnica

Hemos pasado de un simple tablero para un ejecutivo a cientos de modelos y tableros en pocos años. Esto ha traído:

- Dificultad para comprender el contexto.
- Iniciativas que reutilizan datos con diferentes nombres.
- Referencias a tablas que ya no se mantienen.
- Lucha constante por encontrar la "fuente de la verdad".
- "Deuda de datos" por la creación excesiva de tablas personalizadas.

### ¿Cómo Superar la Trampa? 4 Ideas Clave

El stack moderno resolvió problemas de ingeniería (costo y rendimiento), pero generó problemas en cómo los datos se usan para resolver necesidades de negocio. Aquí hay cuatro enfoques de alto nivel para abordar esto:

#### 1. Data Warehousing empieza con entender los datos

No basta con pegar datos de diferentes sistemas (ventas, facturación, usuarios) en una tabla. Primero hay que entender qué significa cada dato y cómo se relaciona.

- **Mapeo Semántico:** Por ejemplo, `client_id` en un sistema puede significar lo mismo que `person_id` en otro. El objetivo es unirlos con sentido. Si entendemos los datos, el Data Warehouse será fácil de entender.

#### 2. Involucrar a los Ingenieros de Software

Los ingenieros de software crean los sistemas y los datos, pero a menudo no saben cómo se usarán después. Esto genera cambios que rompen reportes sin previo aviso.

- **Contratos de Datos:** Define reglas claras de calidad, formato y significado. Los ingenieros deben saber cómo se usan sus datos y ser responsables de la calidad que generan sus sistemas.

#### 3. Los Ingenieros de Datos deben entender el Negocio

Los ingenieros de datos arman la "tubería", pero muchas veces no saben el propósito final.

- **Contexto:** No crees solo una tabla de "ventas"; entiende si se usa para un reporte financiero, una campaña de marketing o una aplicación. Esto permite conectar los datos de forma lógica y útil.

#### 4. Pensar en los Datos como un Producto

Cada tabla, dashboard o modelo debe resolver un problema real de negocio. No debe ser solo un ejercicio técnico.

- Asegúrate de que el producto de datos tenga un cliente claro y un uso útil.

### Conclusión

El stack moderno de datos trae una potencia increíble, pero también trae problemas de calidad y confusión de roles. Es un escenario real en muchas empresas que, en su afán por subirse a la nube, añadieron herramientas sin una estrategia sólida.

Citando el libro _Team Topologies_ de Matthew Skelton: las organizaciones que entienden cómo fluyen sus equipos y sus datos son las que logran escalar sin romperse.

El verdadero stack moderno no es el que tiene más herramientas, es el que logra conectar **datos, personas y decisiones con propósito**.

&nbsp;
