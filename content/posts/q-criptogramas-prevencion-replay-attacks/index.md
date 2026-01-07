---
title: "Pregunta #9 - Criptogramas Dinámicos para Prevención de Replay Attacks"
date: 2025-12-09
summary: Arquitectura de Seguridad en Sistemas de Pago Móvil
tags: ["", "", ""]
author: "Omar Valdez G"
ShowToc: true
weight: 11
draft: false
---

_**Contexto**: Los sistemas de pago digital procesan millones de transacciones diarias donde un atacante podría interceptar datos válidos y reutilizarlos para compras fraudulentas. Apple Pay procesa 41 millones de transacciones diarias usando criptogramas dinámicos que previenen estos ataques._

### PREGUNTA

Estás diseñando un sistema de pagos móviles para una plataforma de e-commerce. Durante una revisión de seguridad, tu equipo identifica que un atacante podría capturar solicitudes NFC válidas y reproducirlas para realizar compras no autorizadas.

Necesitas implementar una solución que genere pruebas criptográficas únicas por transacción.

**¿Cuál es la arquitectura más efectiva para prevenir replay attacks manteniendo capacidad offline?**

&nbsp;

A) Generar un hash SHA-256 del Device Account Number (DAN) y enviarlo en cada transacción junto con un timestamp del servidor

B) Crear un criptograma único combinando DAN + detalles de transacción (monto, merchant ID) + nonce impredecible, validado matemáticamente por la red de pagos

C) Implementar tokens JWT con expiración de 5 minutos firmados con clave simétrica compartida entre dispositivo y servidor

D) Almacenar un contador incremental en el dispositivo que se valida contra una base de datos centralizada en cada transacción

&nbsp;

**RESPUESTA: B**

### EXPLICACIÓN

La opción B implementa el patrón de Per-Transaction Cryptographic Signature que Apple Pay utiliza exitosamente.

El criptograma combina tres elementos críticos: el DAN (token específico del dispositivo almacenado en Secure Element), detalles de la transacción (monto, merchant ID) y un nonce impredecible generado por el terminal de punto de venta.

Esta combinación genera una firma criptográfica única que solo es válida para esa transacción específica.

La red de pagos regenera el criptograma usando su copia del DAN y los mismos parámetros; si coinciden, la transacción es legítima.

Si un atacante captura el criptograma e intenta replicarlo, la validación falla porque el nonce o el monto habrán cambiado.

Este diseño funciona completamente offline porque toda la generación criptográfica ocurre localmente en el dispositivo usando claves pre-establecidas

Las otras opciones tienen vulnerabilidades críticas:

- A depende de timestamps del servidor (requiere conectividad, vulnerable a desincronización de relojes) y un simple hash es insuficiente sin elementos únicos por transacción.
- C requiere conectividad de red para validar JWT y tokens de 5 minutos permiten ventana de replay.
- D necesita acceso constante a base de datos centralizada, eliminando capacidad offline y creando punto único de falla.

### EJEMPLO REAL

Cuando un usuario tapa su iPhone en el metro de Londres sin conexión a internet, el Secure Element genera un criptograma combinando el DAN almacenado localmente, los £10 del viaje, el merchant ID del sistema de transporte, y el nonce enviado por el terminal.

Este criptograma se transmite vía NFC al lector. Posteriormente, cuando el terminal recupera conectividad, envía el criptograma a Visa para validación.

Visa regenera el criptograma usando su copia del DAN y los mismos parámetros de transacción; si coinciden matemáticamente, aprueba el pago.

Si un atacante interceptó ese criptograma e intenta reusarlo, la validación falla instantáneamente porque el nonce ha cambiado o el timestamp es obsoleto.

### CONSEJO CLAVE

En sistemas de alta seguridad, nunca confíes en un solo mecanismo de protección. Implementa defensa en profundidad combinando tokenización (reemplazar secretos), aislamiento de hardware (Secure Element para claves), y validación criptográfica por transacción.

Este enfoque multicapa garantiza que comprometer un componente no expone todo el sistema.

Para APIs de pago, siempre incluye elementos impredecibles (nonces) y específicos de contexto (monto, timestamp) en tus firmas criptográficas.

### REFERENCIAS

- https://www.frugaltesting.com/blog/is-apple-pay-secure-how-apple-ensures-top-tier-security-for-digital-payments - Frugal Testing - Apple Pay Security Architecture: Tokenization & Dynamic Cryptograms
- https://newsletter.systemdesign.one/p/how-does-apple-pay-work - System Design Newsletter - How Apple Pay Handles 41 Million Transactions Securely
- https://docs.nuvei.com/documentation/features/network-tokenization/ - Nuvei Documentation - Network Tokenization & Cryptogram Requirements
