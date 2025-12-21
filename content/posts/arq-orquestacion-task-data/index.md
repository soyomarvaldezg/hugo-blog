---
title: "Pregunta #3 - Arquitecturas de Orquestación: Task-Centric vs Data-Centric"
date: 2025-11-18
summary: ¿Cuándo usar Airflow, Dagster o Prefect según tus necesidades?
tags: ["airflow", "orchestration", "pipelines"]
author: "Omar Valdez G"
ShowToc: true
weight: 5
draft: false
---

_**Contexto**: La elección del orquestador de workflows es una decisión arquitectónica de largo plazo que impacta la escalabilidad de tus pipelines, productividad del equipo y capacidad para adoptar prácticas modernas de gobernanza y observabilidad de datos._

# PREGUNTA

Tu equipo en una fintech está migrando de scripts manuales a un orquestador enterprise. Los requisitos son: ejecutar 450 DAGs con tareas heterogéneas (Python, Bash, Spark, dbt), integración con Kubernetes para ML training, y el equipo de compliance exige trazabilidad completa de linaje de datos desde raw hasta reportes regulatorios sin herramientas externas. El CISO requiere SSO y RBAC en producción, pero el presupuesto solo cubre licencias open source este año.

**¿Qué arquitectura y herramienta deberías seleccionar para balancear estos requisitos contradictorios?**
&nbsp;

A) Airflow – Maneja la heterogeneidad de tareas y tiene SSO/RBAC nativo en la versión open source, pero requerirás implementar linaje manualmente con herramientas como OpenLineage o Marquez

B) Dagster – Proporciona linaje automático y assets como first-class citizens, perfecto para compliance, pero SSO y RBAC están bloqueados en la versión enterprise de pago

C) Prefect – Ofrece orquestación reactiva en tiempo real y arquitectura moderna, pero carece de linaje nativo de datos y las features de seguridad requieren licencia enterprise

D) Kestra – Combina flexibilidad de workflows heterogéneos con YAML declarativo, pero tiene comunidad pequeña (<10 contribuidores activos) y SSO/RBAC solo en versión enterprise

&nbsp;

**RESPUESTA: A**

# EXPLICACIÓN

Airflow es la única opción que satisface el requisito no negociable de seguridad (SSO/RBAC en open source) porque es verdadero software Apache foundation-backed, no open core. Con 31M de descargas mensuales, 77K organizaciones usándolo, y ecosistema maduro para Kubernetes (KubernetesPodOperator usado por 60%+ de usuarios), maneja perfectamente la heterogeneidad de tareas requerida.

El trade-off del linaje manual es manejable: puedes integrar OpenLineage para tracking automático o implementar lineage a medida que compliance lo requiera, sin bloquear el despliegue inicial.

Por qué las otras opciones fallan:

Dagster (B) es técnicamente superior para linaje automático y compliance, pero el modelo open core bloquea SSO/RBAC detrás de paywall enterprise, violando el constraint de presupuesto.

Prefect (C) también usa open core y carece de linaje nativo, requiriendo implementación custom.

Kestra (D) tiene comunidad pequeña (<10 contribuidores) en zona de riesgo para viabilidad long-term y también bloquea features de seguridad.

# EJEMPLO REAL

Uber ejecuta 450K tareas diarias con Airflow, Stripe orquesta 150K tasks daily, y LinkedIn maneja 10K DAGs paralelos simultáneos—todos casos que demuestran la capacidad enterprise-grade de Airflow para escalar con tareas heterogéneas.

Google Cloud Composer y Amazon MWAA (con soporte para Airflow 3.1 desde noviembre 2025) seleccionaron Airflow como estándar managed, validando su madurez arquitectónica.

Para el linaje de datos, organizaciones complementan Airflow con OpenLineage (open source Apache project) obteniendo trazabilidad completa sin licencias enterprise adicionales.

# CONSEJO CLAVE

Antes de comprometerte con un orquestador, verifica la completitud de features en open source versus enterprise. Los modelos open core (Dagster, Prefect, Kestra, Mage) bloquean seguridad crítica (SSO, RBAC, audit logs) detrás de paywalls, creando costos ocultos post-deployment.

Evalúa salud de comunidad con métricas concretas: 20+ contribuidores activos es saludable, <5 es zona de riesgo. Airflow tiene 3K+ contribuidores versus <10 en alternativas emergentes, garantizando sostenibilidad long-term del investment.

# REFERENCIAS

- [State of Apache Airflow 2025 - 31M monthly downloads y 77K organizaciones](https://www.astronomer.io/airflow/state-of-airflow/)
- [Comparativa arquitecturas task-centric vs data-centric en orquestación 2025](https://www.pracdata.io/p/state-of-workflow-orchestration-ecosystem-2025)

- [Airflow 3.1 Human-in-the-Loop workflows para AI - release septiembre 2025](https://www.astronomer.io/blog/introducing-apache-airflow-3-1/)
