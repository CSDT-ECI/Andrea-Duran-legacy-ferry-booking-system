---
title: "Testing Debt"
date: 2024-03-19
---

# Prácticas de Testing Debt en el Sistema de Reserva de Ferry

## Identificación de Prácticas de Testing Debt

Al analizar el código proporcionado, se pueden identificar pocas prácticas de Testing Debt. Estas prácticas demuestran 
que el equipo de desarrollo se ha centrado más en la funcionalidad actual en lugar de invertir tiempo en mejorar 
la base de código y las pruebas. 
A continuación, se detallan las prácticas identificadas junto con ejemplos específicos del código proporcionado.

### 1. Pruebas de Golden Master

Método `compare_to_golden_master()`. 
Este método compara la salida de una ejecución de prueba con un archivo de "golden master" predefinido (que se trata de 
un txt que retorna toda la estructura de la reserva de los ferries definidos). 
Si la salida no coincide con el archivo de referencia, la prueba falla.

- **Ejemplo:**

    ```java
        @Test
        public void compare_to_golden_master() throws IOException {
             WriteToFile("test-run.txt");
             String master = readFile("master.txt");
             String tests = readFile("test-run.txt");
             assertEquals(tests, master);
        }
    ```

### 2. Pruebas de Salida Esperada

El método `first_saff()` en la clase `SaffSqueeze` utiliza una prueba de salida esperada. Esta práctica implica escribir la salida esperada de una función o método en el código de prueba y compararla con la salida real. Si la salida real no coincide con la esperada, la prueba falla.

- **Ejemplo:**
    ```java
        @Test
        public void first_saff() {
        String expectedOutput = "..."; // Salida esperada
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        PrintStream ps = new PrintStream(baos);
        Program.start(ps);
        
             assertEquals(expectedOutput, baos.toString());
        }
    ```
  

### 3. Manejo de Excepciones Inadecuado

En el método `generate_golden_master()`, se captura una excepción `FileNotFoundException` pero no se realiza ninguna 
acción en el bloque `catch`. Esto significa que si ocurre una excepción, el código simplemente la ignorará, 
lo que puede llevar a comportamientos inesperados y dificultar la depuración.

- **Ejemplo:**
    
    ```java
      try {
         ps = new PrintStream(new File(fileName));
         Program.mainWithTestData(ps);
        } catch (FileNotFoundException e) {
        // No se realiza ninguna acción
        } finally {
        ps.close();
        }
    ```

## Pruebas Propuestas para el Sistema de Reserva de Ferry

### Clase `Ferries`

#### Pruebas Existentes

- Carga de ferries desde archivo.
- Creación de objetos `Ferry` a partir de JSON.

#### Pruebas Propuestas

- **Prueba de Carga de Ferries con Archivo Vacío:** 
Verificar que el sistema maneje correctamente un archivo vacío sin lanzar excepciones.

  ```java 
    @Test public void testLoadFerriesFromEmptyFile() { 
    // Preparar un archivo vacío 
    // Llamar a loadFerriesFromFile con el archivo vacío 
    // Verificar que la lista de ferries esté vacía 
    }
  ```

- **Prueba de Carga de Ferries con JSON Inválido:** 
Verificar que el sistema maneje correctamente un archivo con JSON inválido, por ejemplo, un objeto sin el campo requerido "Id".

  ```java 
    @Test public void testLoadFerriesFromInvalidJson() { 
    // Preparar un archivo con JSON inválido
    // Llamar a loadFerriesFromFile con el archivo inválido
    //  Verificar que la lista de ferries esté vacía y que no se haya lanzado ninguna excepción
    }
  ```

---
### Clase `FerryAvailabilityService`

#### Pruebas Existentes

- Determinar el próximo ferry disponible desde un puerto en un momento dado.

#### Pruebas Propuestas

- **Prueba de Disponibilidad de Ferry con Puertos y Horarios Vacíos:** 
Verificar que el sistema maneje correctamente el caso en que no hay ferries disponibles.

    ```java 
        @Test public void testNextFerryAvailableFromEmptyPortsAndTimes() { 
        // Preparar un servicio con listas vacías de puertos y horarios
        // Llamar a nextFerryAvailableFrom con un puerto y horario específicos
        // Verificar que el resultado sea null
        }
    ```
- **Prueba de Disponibilidad de Ferry con Horario de Viaje en el Pasado:** 
Verificar que el sistema no considere ferries con horarios de viaje en el pasado.

    ```java 
        @Test public void testNextFerryAvailableFromPastTime() { 
        // Crear un servicio de disponibilidad de ferry con un horario de viaje en el pasado
        // Llamar a nextFerryAvailableFrom con un puerto y horario específicos
        // Verificar que el resultado sea null
        }
    ```

---
### Clase `FerryManager`

#### Pruebas Existentes

- Creación de un viaje de ferry.
- Adición de un ferry a un viaje.

#### Pruebas Propuestas

- **Prueba de Creación de Viaje de Ferry con Puertos Nulos:** 
Verificar que el sistema maneje correctamente el caso en que los puertos son nulos.
    
  ```java 
      @Test public void testCreateFerryTripWithNullPorts() { 
      // Llamar a createFerryJourney con listas de puertos nulas
      // Verificar que el resultado sea null
      }
  ```
- **Prueba de Adición de Ferry a Viaje con Viaje Nulo:** 
Verificar que el sistema maneje correctamente el caso en que el viaje es nulo.

    ```java 
        @Test public void testAddFerryToJourneyWithNullJourney() { 
        // Llamar a addFerry con un viaje nulo
        // Verificar que se lance una excepción
        }
    ```

---
### Clase `JourneyBookingService`

#### Pruebas Existentes

- Verificación de disponibilidad para reservar un viaje.
- Reserva de un viaje.

#### Pruebas Propuestas

- **Prueba de Disponibilidad de Reserva con Viaje No Existente:** 
Verificar que el sistema maneje correctamente el caso en que se intenta reservar un viaje que no existe.

    ```java 
        @Test public void testCanBookWithNonExistentJourney() { 
        // Preparar un servicio con un viaje no existente
        // Llamar a canBook con el ID del viaje no existente
        // Verificar que el resultado sea false
        }
    ```
- **Prueba de Reserva con Capacidad Insuficiente:** 
Verificar que el sistema maneje correctamente el caso en que se intenta reservar más pasajeros de los disponibles 
en el ferry.

    ```java 
        @Test public void testCanBookWithInsufficientCapacity() { 
        // Llamar a bookJourney con un número de pasajeros mayor que la capacidad del ferry
        // Llamar a canBook con el número de pasajeros que excede la capacidad del ferry
        // Verificar que el resultado sea false
        }
    ```
---
### Clase `PortManager`

#### Pruebas Existentes

- Obtener modelos de puertos con ferries asignados.

#### Pruebas Propuestas

- **Prueba de Obtener Modelos de Puertos con Puertos y Ferries Nulos:** 
Verificar que el sistema maneje correctamente el caso en que los puertos y ferries son nulos.
    
    ```java 
        @Test public void testPortModelsWithNullPortsAndFerries() { 
        /// Preparar un PortManager con listas de puertos y ferries nulas
        // Llamar a PortModels
        // Verificar que el resultado sea una lista vacía
        }
    ```
  
- **Prueba de Obtener Modelos de Puertos con Ferries Sin Puerto de Origen:** 
Verificar que el sistema maneje correctamente el caso en que los ferries no tienen un puerto de origen asignado.

    ```java 
        @Test public void testPortModelsWithFerriesWithoutHomePort() { 
        // Preparar un PortManager con ferries sin puerto de origen asignado
        // Llamar a PortModels
        // Verificar que los ferries no se asignen a ningún puerto
        }
    ```

---
### Clase `Ports`

#### Pruebas Existentes

- Carga de puertos desde archivo.

#### Pruebas Propuestas

- **Prueba de Carga de Puertos con Archivo Vacío:** 
Verificar que el sistema maneje correctamente un archivo vacío sin lanzar excepciones.

    ```java 
        @Test public void testLoadPortsFromEmptyFile() { 
        // Preparar un archivo vacío 
        // Llamar Ports constructor con el archivo vacío 
        // Verificar que la lista de puertos esté vacía
        }
    ```
  
- **Prueba de Carga de Puertos con JSON Inválido:** 
Verificar que el sistema maneje correctamente un archivo con JSON inválido, por ejemplo, un objeto sin el campo requerido "Id".

    ```java 
        @Test public void testLoadPortsFromInvalidJson() { 
        // Preparar un archivo con JSON inválido
        // Llamar a Ports constructor con el archivo inválido
        // Verificar que la lista de puertos esté vacía y que no se haya lanzado ninguna excepción
        }
    ```

---
### Clase `TimeTables`

#### Pruebas Existentes

- Carga de horarios desde archivo.

#### Pruebas Propuestas

- **Prueba de Carga de Horarios con Archivo Vacío:** 
Verificar que el sistema maneje correctamente un archivo vacío sin lanzar excepciones.

    ```java 
        @Test public void testLoadTimeTablesFromEmptyFile() { 
        // Preparar un archivo vacío 
        // Llamar a TimeTables constructor con el archivo vacío 
        // Verificar que la lista de horarios esté vacía
        }
    ```
  
- **Prueba de Carga de Horarios con JSON Inválido:** 
Verificar que el sistema maneje correctamente un archivo con JSON inválido, por ejemplo, un objeto sin el campo requerido "Id".

    ```java 
        @Test public void testLoadTimeTablesFromInvalidJson() { 
        // Preparar un archivo con JSON inválido
        // Llamar a TimeTables constructor con el archivo inválido
        // Verificar que la lista de horarios esté vacía y que no se haya lanzado ninguna excepción
        }
    ```

---
### Clase `TimeTableService`

#### Pruebas Existentes

- Obtener horario de viajes.
- Obtener cruces disponibles.

#### Pruebas Propuestas

- **Prueba de Obtener Horario de Viajes con Puertos Nulos:** 
Verificar que el sistema maneje correctamente el caso en que los puertos son nulos.

    ```java 
        @Test public void testGetTimeTableWithNullPorts() { 
        // Preparar un TimeTableService con listas de puertos nulas
        // Llamar a getTimeTable con puertos nulos
        // Verificar que la lista de filas del horario esté vacía
        }
    ```
  
- **Prueba de Obtener Cruces Disponibles con Horario de Viaje en el Pasado:** 
Verificar que el sistema no considere cruces con horarios de viaje en el pasado.

    ```java 
        @Test public void testGetAvailableCrossingsWithPastTime() { 
        // Preparar un TimeTableService con horarios de viaje en el pasado
        // Llamar a getAvailableCrossings con un horario de viaje en el pasado
        // Verificar que el resultado sea una lista vacía
        }
    ```
---

## Mejoras Propuestas

### 1. Implementación de Pruebas de Integración

- **Pruebas de Integración:** Añadir pruebas de integración para verificar la interacción correcta entre diferentes 
componentes del sistema, como la interacción entre `PortManager`, `FerryAvailabilityService`, y `TimeTableService`.

### 2. Pruebas de Carga y Rendimiento

- **Pruebas de Carga:** Implementar pruebas de carga para simular un alto número de usuarios y verificar que el sistema 
se comporta correctamente bajo estrés.
- **Pruebas de Rendimiento:** Realizar pruebas de rendimiento para identificar cuellos de botella y optimizar el sistema.

### 3. Pruebas de Usabilidad

- **Pruebas de Usabilidad:** Realizar pruebas de usabilidad para asegurar que la interfaz de usuario es intuitiva y fácil de usar.

### 4. Pruebas de Compatibilidad

- **Pruebas de Compatibilidad:** Añadir pruebas de compatibilidad para verificar que el sistema funciona correctamente en diferentes navegadores, dispositivos y sistemas operativos.

### 5. Pruebas de Regresión

- **Pruebas de Regresión:** Implementar un sistema de pruebas de regresión para asegurar que los cambios en el código no rompen funcionalidades existentes.

### 6. Automatización de Pruebas

- **Automatización de Pruebas:** Automatizar las pruebas unitarias, de integración, de carga, de rendimiento, de seguridad, de usabilidad, de accesibilidad, y de compatibilidad para facilitar su ejecución y asegurar que se ejecuten regularmente.

### 7. Mejoras en la Cobertura de Pruebas

- **Mejorar la Cobertura de Pruebas:** Aumentar la cobertura de las pruebas para asegurar que se prueben todos los casos de uso y escenarios posibles.
- **Pruebas de Borde:** Implementar pruebas de borde para identificar problemas en los límites de los casos de prueba.


