---
title: "Deuda Técnica en Arquitectura"
date: 2024-04-28
---

# Deuda Técnica en Arquitectura

Este documento describe algunos de los olores arquitectónicos identificados en el sistema de reserva de ferries heredado. 
Estos olores pueden indicar áreas del sistema que podrían beneficiarse de una refactorización o reestructuración para mejorar la mantenibilidad, escalabilidad y extensibilidad.

## 1. Acoplamiento de Clases

- **Descripción**: El sistema muestra un alto grado de dependencia entre clases, lo que dificulta la modificación de una parte del sistema sin afectar a otras.
- **Ejemplos**: La clase `Booking` depende directamente de la clase `Ferry`, lo que limita la flexibilidad para cambiar la implementación de los ferries sin afectar a la lógica de reserva.

## 2. Herencia Incorrecta

- **Descripción**: El uso de herencia donde no es apropiado puede llevar a una jerarquía de clases confusa y difícil de mantener.
- **Ejemplos**: La clase `Ferry` tiene subclases como `ExpressFerry` y `RegularFerry`, pero estas subclases no parecen aprovechar completamente las ventajas de la herencia, lo que sugiere un diseño incorrecto.

## 3. Gran Objeto

- **Descripción**: Objetos que tienen demasiadas responsabilidades pueden ser difíciles de mantener y entender.
- **Ejemplos**: La clase `BookingSystem` maneja tanto la lógica de reserva como la gestión de disponibilidad de los ferries, lo que viola el principio de responsabilidad única.

## 4. Datos Duplicados

- **Descripción**: La duplicación de datos puede llevar a inconsistencias y dificultades para mantener la integridad de los datos.
- **Ejemplos**: La información sobre los ferries, como la capacidad y la ruta, se almacena tanto en la base de datos como en el código, lo que puede llevar a discrepancias.

## 5. Configuración Fuerte

- **Descripción**: Depender demasiado de la configuración externa puede hacer que el sistema sea difícil de entender y mantener.
- **Ejemplos**: El sistema utiliza archivos de configuración externos para definir la disponibilidad de los ferries, lo que complica la comprensión del flujo de datos y la lógica de negocio.

## 6. Funciones Grandes

- **Descripción**: Funciones o métodos que son demasiado largos o complejos pueden ser difíciles de entender y mantener.
- **Ejemplos**: El método `calculateFare` en la clase `Booking` es muy largo y realiza múltiples cálculos, lo que dificulta su comprensión y mantenimiento.

## 7. Gestión de Excepciones

- **Descripción**: La gestión de excepciones debe ser estándarizada y minimizar la divulgación de información en caso de una excepción.
- **Ejemplos**: El sistema no implementa un manejo de excepciones estructurado, lo que puede llevar a la divulgación de información sensible en caso de errores.

## 8. Autenticación y Autorización

- **Descripción**: Los límites de confianza de la aplicación deben ser identificados y se deben utilizar cuentas con privilegios mínimos.
- **Ejemplos**: El sistema no implementa una política de uso de cuentas con privilegios mínimos, lo que puede aumentar el riesgo de ataques.

## 9. Auditoría y Registro

- **Descripción**: El diseño debe identificar el nivel de auditoría y registro necesario para la aplicación, incluyendo la seguridad y análisis de los archivos de registro.
- **Ejemplos**: El sistema no tiene un diseño claro para el almacenamiento, seguridad y análisis de los archivos de registro, 
lo que puede dificultar la auditoría y la detección de problemas de seguridad.

## 10. Consideraciones de Despliegue e Infraestructura

- **Descripción**: El diseño debe considerar las políticas de seguridad de la empresa, las restricciones de seguridad de la infraestructura y las necesidades de configuración de despliegue.
- **Ejemplos**: El sistema no tiene en cuenta las restricciones de seguridad impuestas por la infraestructura o las necesidades específicas de despliegue, 
lo que puede afectar la seguridad y la escalabilidad.
