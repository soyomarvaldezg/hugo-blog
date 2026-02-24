---
title: "ELT a Escala: Por Qué Probar en Producción Está Matando el Data Engineering"
date: 2026-02-22
summary: "ELT revolucionó los pipelines de datos al mover las transformaciones al warehouse, pero creó un defecto crítico: los ingenieros de datos ahora prueban todo en producción sin entornos de desarrollo apropiados, haciendo que el debugging sea costoso y el mantenimiento casi imposible a escala."
tags: [dataengineering, elt, etl, bestpractices, devops, dlt, staging, testing]
author: "Omar Valdez G"
weight: 2
ShowToc: true
draft: false
---

## TL;DR

- ELT hizo fácil construir pipelines pero casi imposible mantenerlos a escala debido a la falta de entornos de desarrollo
- Los ingenieros de datos carecen de herramientas básicas de desarrollo que los ingenieros de software dan por hecho: staging, CI/CD, pruebas locales y bucles de feedback rápidos
- La industria optimizó para el problema incorrecto—escalar almacenamiento y cómputo en lugar de escalar prácticas y flujos de trabajo de desarrollo
- Las soluciones actuales (más observabilidad, orquestación, gobernanza) son todas reactivas y abordan síntomas en lugar de causas raíz
- La solución real requiere una capa de staging para transformaciones y herramientas de desarrollo local, no más monitoreo

Si deseas escuchar en vez de leer, [haz click aquí](https://open.spotify.com/episode/6Oiz4n5rEoq7A9zOc4ZMAx?si=HT59VOsqRTa83NZ1s1JfBg)

---

## Intro

Hay algo que hemos normalizado en data engineering que sería completamente inaceptable en software. Si le dijeras a un equipo de backend que tienen que probar cada cambio directamente en producción—con datos reales, costos reales, consecuencias reales—pensarían que estás siendo irresponsable. Sin embargo, en data engineering, no llamamos a esto "probar en producción." Lo llamamos ELT.

Esto no vino de la teoría. Vino de ver el mismo patrón repetirse en docenas de equipos: equipos pequeños y capaces que se mueven rápido al principio—dashboards aquí, pipelines allá, todo parece funcionar—durante unos nueve meses. Entonces algo se rompe. Los dashboards se vuelven lentos. Los cambios pequeños se vuelven aterradores. El warehouse se bloquea por minutos, a veces horas.

Cuando preguntas dónde prueban antes de desplegar, la respuesta es incómoda: "Aquí mismo. En producción." No porque quieran, sino porque no hay otra opción real.

Esto no es un problema de herramientas. Es un problema de modelo mental.

---

## Lo Que ELT Hizo Bien

ELT nos da algo poderoso: velocidad inicial. Mover datos rápido, transformar en SQL, aprovechar warehouses masivos como Snowflake y Redshift—eso fue una revolución real. ETL requería ingeniería especializada en Java o Python para cada pipeline. Las bases de datos MPP hicieron SQL performante a escala. Esto cambió la propiedad de ingenieros de backend a equipos de analítica y simplificó la ingesta a operaciones básicas de copia, acelerando el tiempo para insight.

El enfoque democratizó la transformación de datos más allá de ingenieros especializados, eliminó el cuello de botella del desarrollador para cambios de esquema, y permitió a los equipos de analítica ser dueños de sus propios pipelines. Ciclos de iteración más rápidos para el desarrollo inicial. Eso es lo que ELT hizo bien.

---

## El Costo Oculto

Pero ELT también creó algo silencioso: el warehouse se convirtió simultáneamente en entorno de desarrollo y entorno de producción. Sin darnos cuenta, normalizamos un flujo de trabajo donde cada prueba cuesta dinero, cada error tiene consecuencias reales, y cada trabajo bloqueado afecta a otros equipos.

Al principio, esto no se siente mal. Se siente práctico. Hasta que deja de escalar.

Cuando tienes 10-20 modelos corriendo, todo es manejable. El problema es que el sistema no te avisa cuando deja de escalar. Si no tienes las herramientas o la cultura correspondiente, una vez que llegas a 300 modelos, ya estás atrapado. Ya es muy complicado.

El costo no es solo dinero. Es atención y riesgo. El costo real es cognitivo. Empiezas a pensar dos veces antes de tocar algo. No porque faltes de habilidad, sino porque temes romper algo downstream sin darte cuenta. Cada cambio se vuelve muy tenso. Cada decisión pesa más de lo necesario.

---

## El Breakdown de Escala

He visto este patrón repetirse cuando los equipos pasan de 50 a 500 transformaciones. ¿Qué pasa? Dejas de construir cosas nuevas y solo mantienes lo que hay. El sistema se vuelve frágil. Regresas a lo defensivo. Es común escuchar: "Mejor no tocarlo" o "Corrámoslo en la noche cuando nadie esté ahí."

Cuando muchas personas quieren probar cosas al mismo tiempo, el warehouse se convierte en un cuello de botella humano. Esto no es técnico—es humano.

Este patrón aparece en la historia. Con el tiempo, emerge una tensión. Siempre eliges entre dos malas opciones: ser rápido y arriesgarte a romper algo, o ir lento y bloquear a todos los demás. No hay buenas opciones. Esto afecta la confianza del equipo en el sistema, incluso en el sistema que ellos mismos crearon.

La gente pierde confianza. Empiezan a hacer workarounds. Saben que si no lo tocan, no lo van a romper.

---

## Por Qué Las Soluciones Reactivas Fallan

Por mucho tiempo, la respuesta de la industria ha sido: más observabilidad, más orquestación, más gobernanza. Esto debería ser lo opuesto. Estamos agregando políticas y procesos que no abordan el problema raíz—seguridad, confiabilidad y precisión de los datos.

Todo esto es reactivo. Detecta el problema después de que ocurre. Los dashboards de observabilidad te dicen qué falló, no que tu modelo de flujo de trabajo es el problema.

Es como instalar una cámara de seguridad. Te muestra el robo, pero no lo previno. En data engineering, confundimos visibilidad con prevención.

---

## La Solución Real

La clave es esta: el problema no es ELT en sí. Es ELT sin entornos de staging.

La ingeniería de software resolvió esto hace mucho tiempo: dev local, staging, CI/CD, producción. Llámalo como quieras, pero tiene esas etapas. En datos, por alguna razón—no todos los equipos, no todas las empresas, pero muchos que he visto—esas etapas se saltan de ingeniería y desarrollo.

Aprendemos cuando este tipo de sistema se rompe. No deberíamos dejar de aprender porque ya está resuelto. Por alguna razón, no lo incluimos en nuestros flujos de trabajo de datos. Quizás es porque los datos involucran muchos temas humanos, así que los tratamos diferente al software.

---

## Qué Cambia Cuando Separas Desarrollo de Producción

Cuando realmente separas desarrollo de producción, el equipo se siente más seguro experimentando, iterando más rápido, aprendiendo sin miedo. La conversación cambia de "veamos qué se rompe hoy" a "probémoslo primero en staging." Eso cambia todo.

Cuando realmente vives esta práctica y ves los beneficios, no hay retorno. Nunca. Y no debería haberlo. Nadie quiere volver a probar en producción.

Esto es más cultural que técnico. Las herramientas existen. Lo difícil es admitir que la forma en que operamos o el modelo actual no está diseñado para escala. Es doloroso. Entiendo por qué los equipos lo evitan.

---

## Es una Postura, No una Herramienta

El cambio real no es una herramienta. Las herramientas son muchas. Es una postura. Primero, aceptar que los datos también son software. Que las transformaciones merecen el mismo rigor a pesar de tener lógica de negocio y contexto que es diferente de algo determinista o con estado. Los datos tienen memoria, sí. Pero merecen el mismo rigor de ingeniería.

Un equipo de datos maduro no trata de ir más lento. Trata de dejar de pagar por aprender en producción. Los entornos separados no son burocracia. Son lo que te permite pensar con claridad, escalar, hacer las cosas bien desde el principio.

Toma más tiempo, sí. Pero hacer las cosas bien tiene sus beneficios. Siempre pasa lo mismo. Pero por alguna razón, todos tendemos a querer resultados ya. Lo antes posible. Queremos ver cambios inmediatos. Las cosas buenas toman su tiempo.

Aquí hay un principio: cuando construyes un pipeline y vas a escalar, no puedes probarlo con miedo. Si pruebas con miedo, el sistema está mal diseñado. Probar en producción debería ser simple—simple porque ya hiciste las validaciones previas. También es, de cierta forma, responsable a largo plazo. Si no lo haces bien, se va a romper.

---

## Cómo Vender el Cambio Sin Hablar de Herramientas

¿Cómo vender esta idea sin caer en la trampa de las herramientas? Habla de riesgo y costos invisibles. Es muy difícil, pero sígueme aquí: costos invisibles. Tiempo perdido. No estoy mencionando tecnología. Costos invisibles. Tiempo perdido.

Cuando empiezas a operar con entornos apropiados, te das cuenta de que la duda no aparece hasta el momento en que ya no puedes avanzar más. El enfoque anterior te paraliza porque lo hiciste de esa manera y es muy complicado avanzar. Esto debería redefinir tu forma de pensar sobre el software de datos.

La medida de un equipo de datos no es cuántos dashboards tienes. Es qué tan seguro es cambiar el sistema que ya tienes. Esa es la perspectiva diferente que puedes adoptar. Si tienes muchas herramientas, ¿por qué no puedes moverte más rápido? Si tienes muchos dashboards, por qué no eres mejor en lo que haces?

La forma en que operas, incluyendo el sistema que necesitas afectar, esa es la diferencia.

---

## La Realidad de Escala

No es normal probar cambios críticos directamente en producción. Solo lo normalizamos porque al principio funcionaba. Era rápido. Era fácil. Solo descargabas los datos, los cargabas en el warehouse y transformabas ahí. No mencionamos qué podría pasar. No teníamos alternativas claras.

Cuando los sistemas crecen, la falta de entornos empieza a mostrar. No solo se siente que falta algo. Te quita la capacidad de pensar. Tu tiempo, tu energía, tu forma de operar cambia. Estás pensando: "Voy a probar con miedo. Miedo de que si hago esto, ¿cuánto costará? ¿Bloqueará el sistema? ¿Afectará a la persona que necesita este reporte para estos stakeholders?"

Cada cambio se vuelve muy tenso. Cada decisión pesa más de lo necesario. No debería ser así.

El software de datos puede manejar mucho de esto. ELT no está roto. Pero escalar ELT sin staging, sin desarrollo local, sin esos entornos nos fuerza a aprender de la forma más costosa posible. Terminas pagando miles de dólares en costos de warehouse, tiempo de debugging y costo de oportunidad. Y no debería ser así.

Esto no es una limitación técnica. Hay muchos equipos brillantes haciendo cosas increíbles, pero todo es frágil. Da un paso atrás. Es una decisión de diseño. Ese es el tema primordial.

---

## Cierre

Si estás viviendo esta realidad—si estás probando cada cambio en producción, si tienes miedo de tocar los pipelines existentes, si estás pagando miles en costos de debugging—sabe que hay otra forma.

Entornos de staging reales. Capacidades de desarrollo local. Flujos de trabajo de promoción CI/CD. Estos no son lujos reservados para empresas de software maduras. Son necesidades para cualquier equipo de datos que quiera escalar sin romperse.

Si tienes un entorno de staging real, o si ya pasaste ese punto frágil y quieres compartir tu experiencia, contáctame. Comparte esta información. Estos tipos de conversaciones son las que realmente mueven esta disciplina adelante. Cuando los equipos planifican de la mejor manera posible, sucede algo increíble otra vez. Algo muy bueno. Algo excelente.

---

## Fuentes

- [Staging Environments for Data Pipelines — DLT Hub](https://dlthub.com/blog/staging)
- Mejores prácticas de la industria de organizaciones que gestionan decenas de miles de pipelines de datos a escala
