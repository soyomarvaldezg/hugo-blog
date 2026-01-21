---
title: "Tu query no devuelve nada? Culpa a NULL 🤫"
date: 2025-11-23
summary: NULL no es una cadena vacía, es la ausencia de valor. Aprende por qué tus filtros fallan al usar comparaciones comunes, cómo usar `IS NULL` correctamente y evita errores silenciosos en tus consultas SQL.
tags: ["sql", "tutorial", "null", "data engineering"]
author: "Omar Valdez G"
weight: 2
ShowToc: true
draft: false
---

### TL;DR

El error más silencioso en SQL es tratar `NULL` como una cadena vacía o como un valor comparable. `NULL` representa la **ausencia de dato**. Comparar con `=` o `!=` devolverá siempre "falso" (o desconocido), haciendo que tu query no devuelva resultados aunque existan datos. Para filtrar correctamente, debes usar los operadores `IS NULL` y `IS NOT NULL`. Ten cuidado también con las funciones de agregación como `COUNT` y con los `JOINS`.

Video en YouTube: [click aquí](https://youtu.be/fkwaczXYrCI?si=IEAqJ1f60d_JrvYQ)

### Intro

¿Alguna vez has ejecutado una consulta SQL pensando que devolvería datos, y el resultado ha sido un set vacío, pero sin ningún mensaje de error? Este es el error más común y silencioso en el desarrollo de bases de datos.

No va a "crashear" tu aplicación ni lanzar una excepción, simplemente te devuelve datos incompletos. En este artículo, entenderás la diferencia fundamental entre `NULL` y una cadena vacía, y aprenderás a escribir queries que realmente traen toda la información que necesitas.

### El Escenario: Buscando lo "Vacío"

Imagina que tenemos una tabla simple llamada `usuarios`. Algunos usuarios tienen un segundo apellido y otros no. En la base de datos, aquellos que no tienen segundo apellido no deben guardarse como un espacio en blanco o una cadena vacía, sino como `NULL`.

Si intentamos buscar a los usuarios que _no_ tienen segundo apellido (Ana y Carlos), el primer instinto de muchos desarrolladores es buscar donde el apellido es una "cadena vacía".

**El Query Incorrecto:**

```sql
SELECT *
FROM usuarios
WHERE segundo_apellido = '';
```

Al ejecutar esto, el resultado es `0 filas`. ¿Por qué? Si sabemos que Ana y Carlos no tienen segundo apellido, ¿por qué no aparecen?

### ¿Qué es realmente NULL?

La confusión viene de pensar que `NULL` es igual a `""` (cadena vacía). **No lo es.**

`NULL` no es un valor; es la **ausencia de un valor**. No es cero, no es un espacio en blanco, es simplemente "desconocido". Por lo tanto, la igualdad matemática o lógica no aplica aquí.

SQL maneja una **lógica de tres estados**:

1.  `TRUE` (Verdadero)
2.  `FALSE` (Falso)
3.  `UNKNOWN` (Desconocido)

Cualquier comparación directa con `NULL` (como `= NULL`, `<> NULL`, o incluso `NULL = NULL`) resulta en `UNKNOWN`. La cláusula `WHERE` solo deja pasar las filas donde la condición es `TRUE`. Si es `UNKNOWN`, la fila se filtra.

### La Solución: IS NULL

Para preguntarle a SQL por la ausencia de un valor, no usamos comparación, usamos una **comprobación de estado**.

**El Query Correcto:**

```sql
SELECT *
FROM usuarios
WHERE segundo_apellido IS NULL;
```

Este comando funciona porque `IS NULL` explícitamente devuelve `TRUE` si el valor es nulo.

**Lo opuesto también aplica:**
Si quieres filtrar los que SÍ tienen valor, no uses `<> ''` (ya que un NULL también pasaría el filtro en algunos contextos o fallaría en otros dependiendo del motor), usa:

```sql
SELECT *
FROM usuarios
WHERE segundo_apellido IS NOT NULL;
```

### Otras Trampas con NULL

El problema de `NULL` no solo afecta a los filtros `WHERE`. Hay otros dos lugares comunes donde te puede jugar una mala pasada:

#### 1. Funciones de Agregación (`COUNT`)

Ten mucho cuidado al contar filas.

- `COUNT(*)`: Cuenta el número total de filas en la tabla.
- `COUNT(columna)`: Cuenta solo las filas donde esa columna **NO** es nula.

Si haces `COUNT(segundo_apellido)` en nuestra tabla, te dará `2` (solo los que tienen apellido), en lugar de `4` (el total de usuarios). Si quieres contar usuarios independientemente de si tienen apellido o no, usa `COUNT(*)`.

#### 2. Joins (Uniones)

Al unir tablas, si no manejas los `NULLs` correctamente en las llaves de unión, puedes perder datos inesperadamente.

- En un `INNER JOIN`: Si una columna de unión es `NULL` en una de las tablas, esa fila no coincidirá con nada (ya que `NULL = NULL` es falso) y se perderá del resultado.
- En un `LEFT JOIN`: Si la tabla derecha tiene `NULLs`, aparecerán en el resultado, pero debes tener cuidado de no filtrarlos después en tu cláusula `WHERE`.

### Conclusión

Cambiar la mentalidad sobre `NULL` es crucial para escribir SQL robusto. Recuerda siempre:

1.  `NULL` es la **ausencia** de valor, no un valor vacío.
2.  Nunca uses `=` o `!=` o `<>` para comparar con `NULL`.
3.  Usa siempre `IS NULL` y `IS NOT NULL`.
4.  Ten presente cómo afecta `NULL` a tus conteos (`COUNT`) y tus uniones (`JOINS`).

La regla es simple: Siempre que trabajes con columnas que pueden ser nulas, piensa en términos de presencia o ausencia (`IS`), no de igualdad (`=`).

&nbsp;
