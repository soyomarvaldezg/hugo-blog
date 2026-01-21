---
title: "Cómo Nació la Arquitectura de Datos Moderna"
date: 2025-03-18
summary: Un recorrido histórico desde los Data Warehouses relacionales on-premise hasta los Data Lakes y el moderno Data Lakehouse. Descubre cómo la nube y el Big Data cambiaron las reglas del juego.
tags: ["history", "architecture", "cloud", "big data", "lakehouse"]
author: "Omar Valdez G"
weight: 2
ShowToc: true
draft: false
---

### TL;DR

La arquitectura de datos ha evolucionado desde sistemas rígidos y costosos (Data Warehouse on-premise) hacia almacenamientos masivos y desordenados (Data Lakes), hasta llegar a soluciones híbridas en la nube. Hoy en día, el **Data Lakehouse** combina lo mejor de ambos mundos: el almacenamiento barato de un lago con las capacidades de gestión transaccional de un warehouse, apoyándose en **metadatos** y **APIs** para mantener el orden.

Video en Youtube: [click aquí](https://youtu.be/lMP7U6HCTQk?si=2yG6pPchCK27S9zg)

### Intro

¿Te has preguntado por qué antes los datos cabían en un solo servidor y ahora necesitamos la complejidad de la nube? La respuesta está en la evolución de las necesidades del negocio y el volumen de información.

Antes de profundizar en la historia, definamos qué es arquitectura de datos: son los planos y planes. La estrategia. Es vital tener un plan de organización, almacenamiento y explotación de la información.

Para usar una analogía: imagina tu cocina. Si no ordenas tu despensa, pasarás tiempo buscando qué compraste, qué vas a comer, y es probable que la comida se caduque. Con los datos pasa lo mismo. Sin arquitectura, gastas tiempo implementando soluciones que creías correctas pero que terminan siendo inútiles.

### Fase 1: Data Warehouse Relacional (On-Premise)

En los inicios, el problema principal siempre fue: ¿Cómo acceder a la información correcta? Los Data Warehouses on-premise surgieron para resolver esto, enfocados en Business Intelligence (BI).

El primer Data Warehouse fue instalado por Teradata en 1983 para análisis financiero.

**El flujo de esta arquitectura seguía un patrón clásico de ETL:**

1.  **Fuentes:** Ventas, inventario, CRM.
2.  **Staging:** Un área intermedia para transformar los datos.
3.  **Data Warehouse:** El repositorio final con los datos estructurados.

**El problema:**
Era una arquitectura rígida. Si necesitabas más poder de almacenamiento o cómputo, implicaba meses de inversión y cambios en la infraestructura física. Cambiar el modelado de datos era casi como reconstruir la casa desde cero.

### Fase 2: Data Lake

Con la llegada del Big Data alrededor de 2010 (con Hadoop, MapReduce y Spark), el objetivo cambió radicalmente: almacenar todo en **raw** (crudo) a bajo costo.

**El flujo del Data Lake era más simple:**

1.  **Fuentes:** Archivos planos, videos, audio, logs.
2.  **Data Lake:** Un repositorio central donde se tiraba todo "tal cual".

El Data Lake se vendió como la solución a todos los problemas del Warehouse relacional. Sin embargo, surgió un nuevo problema: consultar los datos no era fácil.

**El "Data Swamp" (Pantano de Datos):**
A menudo, un Científico de Datos se encontraba con que los datos estaban ahí, pero no había metadatos ni catálogos. Nadie sabía qué estaba guardado ni cómo encontrarlo. La mentalidad de "como es barato, guardemos todo" sin estrategia llevó a un caos de gestión. Se almacenaba la información, pero no se evaluaba _cómo_ administrarla.

### Fase 3: La Nube y el Modern Data Warehouse

Con la popularización de la nube, cambió la forma de pensar: ya no hace falta comprar servidores, solo pagas por lo que usas. Esto permitió manejar la **Velocidad** (streaming, tiempo real) y el **Volumen** de datos que empresas como Netflix generan (millones de eventos por clic).

Aquí surge el concepto del **Modern Data Warehouse**. Se dio cuenta de que los Data Lakes fallaron en reemplazar totalmente al Data Warehouse relacional, pero ofrecían un excelente lugar de aterrizaje (landing zone).

La solución fue combinar lo mejor de los dos mundos:

1.  **Fuentes:** Diversos orígenes de datos.
2.  **Ingesta -> Data Lake:** Se guarda la información cruda.
3.  **Transformación -> Data Warehouse:** Se mueven los datos necesarios a una estructura relacional para análisis.
4.  **Visualización:** Herramientas de BI.

Esta arquitectura permitía tener almacenamiento barato en el lago y rendimiento estructurado en el warehouse.

### Fase 4: Data Lakehouse

Alrededor de 2020, la evolución dio un paso más con el **Data Lakehouse**. El objetivo era eliminar la necesidad de tener un Data Warehouse relacional separado y utilizar un solo repositorio.

¿Qué cambió para que el Data Lake no fuera un caos como antes?
La aparición de una **capa de software de almacenamiento transaccional** que se ejecuta sobre el Data Lake existente. Esta capa hace que el lago funcione como una base de datos tradicional (propiedades ACID), pero manteniendo la flexibilidad de guardar cualquier tipo de dato.

**Diagrama de un Data Lakehouse:**

1.  **Fuentes:** Varias fuentes de datos.
2.  **Ingesta -> Repositorio:** Todo cae en un solo lugar.
3.  **Capa de Software:** Aquí está la magia, la capa transaccional sobre el lago.
4.  **Modelado:** Se preparan los datos.
5.  **Visualización:** Consumo final.

### Componentes Transversales Clave

Para que un Data Lakehouse funcione correctamente, se necesitan dos componentes fundamentales que atraviesan todo el flujo:

1.  **Metadatos:**
    Es la información descriptiva (definiciones, linaje, catálogos).
    - **Ingesta:** Describe el origen y estructura.
    - **Transformación:** Rastrea qué se está haciendo.
    - **Visualización:** Permite a los usuarios finales saber con qué están tratando.

2.  **APIs:**
    Facilitan la exposición y consumo de datos. Durante la transformación, se pueden exponer APIs para acceder a datos limpios directamente, permitiendo una integración más fluida con otras aplicaciones.

### Mención Especial: Data Mesh

Es importante mencionar el **Data Mesh**, aunque no es una tecnología per se, sino un cambio de mentalidad organizacional. No todas las empresas están listas para implementarlo; depende de su nivel de madurez. Es un tema profundo que abordaremos en otro video, pero debes saber que evaluar si es necesario es parte del rol del arquitecto.

### Conclusión

Las arquitecturas de datos siempre han evolucionado para resolver problemas. Sin embargo, cada mejora trae nuevos desafíos.

Como menciona el libro _The Innovator's Dilemma_, la clave está en responder a los retos con soluciones que nos impulsen a evolucionar en lugar de quedarnos con lo que ya conocemos. En el mundo de los datos, la única constante es el cambio. No se trata de alcanzar una meta final, sino de seguir innovando y aprendiendo paso a paso.

&nbsp;
