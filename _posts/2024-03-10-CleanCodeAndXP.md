---
title: "Clean Code y Prácticas XP"
date: 2024-03-10
---

# Análisis del Sistema de Reserva de Ferry

## Características de Clean Code Observadas

- **Nombramiento Significativo (Principio del menos asombro):** En general, los nombres de variables, métodos y clases son descriptivos y expresan claramente su propósito. Por ejemplo:


    public class FerryAvailabilityService {
    
        public Ferry nextFerryAvailableFrom(int portId, long time) {
            // ...
        }
    
    }


- **Formato consistente:** El código está formateado de manera consistente, utilizando indentación, espacios en blanco y saltos de línea para mejorar la legibilidad. Tal y como se puede ver en los ejemplos anteriores.


## Características de Clean Code No Cumplidas


- **Código Enfocado:** La mayoría de las funciones son grandes y no enfocadas, como se menciona a detalle en el archivo
  [Code Smells y Propuestas de Refactoring](2024-03-03-CodeSmellsAndRefactoring.md). Un ejemplo de estas grandes funciones que se pueden dividir se muestra a continuación:

      public class JourneyBookingService {
    
        public boolean canBook(int journeyId, int passengers) {
          List<TimeTable> timetables = timeTables.all();
          List<TimeTableEntry> allEntries = new ArrayList<TimeTableEntry>();
          for (TimeTable tt : timetables) {
              allEntries.addAll(tt.entries);
          }
          Collections.sort(allEntries, new Comparator<TimeTableEntry>() {

              @Override
              public int compare(TimeTableEntry tte1, TimeTableEntry tte2) {
                  return Long.compare(tte1.time, tte2.time);
              }
          });

          for (TimeTableEntry timetable : allEntries) {
              Ferry ferry = ferryService.nextFerryAvailableFrom(timetable.originId, timetable.time);

              if (timetable.id == journeyId) {
                  List<Booking> journeyBookings = new ArrayList<>();
                  for (Booking x : bookings.all()) {
                      if (x.journeyId == journeyId) {
                          journeyBookings.add(x);
                      }
                  }
                  int pax = 0;
                  for (Booking x : bookings.all()) {
                      pax += x.passengers;
                  }
                  int seatsLeft = ferry.passengers - pax;
                  return seatsLeft >= passengers;
              }
          }
          return false;
          }
        }


- **Abstracción:** Hay muchas clases cortas, sin embargo, podrían extenderse o usarse de mejor forma, porque hay clases sin métodos, que solo representan atributos. Por ejemplo:

      public class Port {
          public int id;
          public String name;
      }


- **Escalable:** No cumple con los principios SOLID ni con otros. No es muy escalable.


- **Testeable:** No se implementan pruebas unitarias signiticativas. Son demasiado largas y no son muy dicientes.


- **Entendible:** La clase `Program` es muy larga y hay métodos con gran complejidad e innecesario, puesto que usan el programa
  para ejecutar pruebas que podrían ser automatizadas.

- **Duplicidad:** Hay código duplicado en la forma en que se manejan las listas de `Booking`.

- **Documentación:** No hay documentación del código.


## Recomendaciones para Mejorar el Código

1. **División de Funciones**: La función `canBook` es un buen candidato para ser dividida en funciones más pequeñas y enfocadas. Por ejemplo, la lógica para obtener todos los `TimeTableEntry` podría ser extraída a una función separada.
   Del mismo modo, la lógica para calcular los asientos disponibles podría ser otra función. Esto mejora  a legibilidad y facilita la prueba de cada parte del código de manera independiente.


2. **Extensión de Clases**: Para las clases como `Port` que solo contienen atributos, se puede considerar agregar métodos que encapsulen la lógica relacionada con esos atributos.
   Esto no solo mejora la abstracción sino que también hace que el código sea más fácil de mantener y extender.


3. **Implementación de Pruebas Unitarias**: Añadir pruebas unitarias significativas para cada componente del sistema. Esto incluye pruebas para los casos de éxito,
   los casos de error y los casos límite. Las pruebas deben ser concisas y claras, cubriendo tanto la funcionalidad esperada como los casos de borde.


4. **Refactorización de la Clase `Program`**: La clase `Program` parece ser un punto de entrada para ejecutar pruebas o demostraciones.
   Se considera dividir esta clase en funciones más pequeñas y enfocadas, y mover la lógica de prueba a clases de prueba separadas.
   Esto mejora la legibilidad y hace que el código sea más fácil de mantener.


5. **Documentación**: Añadir comentarios y documentación al código para explicar la funcionalidad, los propósitos de las clases y métodos, y cualquier lógica compleja. Esto es especialmente importante para las funciones y clases que no son inmediatamente claras a primera vista.

---
## Principios de Programación NO Aplicados


### Principios SOLID

#### Principio de Responsabilidad Única (SRP)
- **Violación**: Algunas clases como `Ferries`, `PortManager`, `FerryAvailabilityService`, `JourneyBookingService`, `TimeTables` y `PortModels` tienen múltiples responsabilidades.
- **Recomendación**: Dividir las clase en varias clases más pequeñas, cada una con una única responsabilidad.

#### Principio Abierto/Cerrado (OCP)
- **Violación**: La clase `FerryManager` tiene un método `createFerryJourney` que está diseñado para manejar un caso específico.
  La clase `TimeTableService` tiene un método `getTimeTable` que está diseñado para manejar un caso específico de visualización de horarios.
- **Recomendación**: Utilizar una interfaz o una clase abstracta para definir la creación de viajes de ferry.
  Introducir una interfaz o una clase abstracta para definir la visualización de horarios, permitiendo que diferentes implementaciones manejen diferentes tipos
  de visualizaciones sin modificar la clase `TimeTableService`.


#### Principio de Sustitución de Liskov (LSP)
- **Violación**: No se observa una violación directa de LSP en el código proporcionado.
- **Recomendación**: Asegurarnos de que cualquier extensión de clases base se mantenga dentro de los límites definidos por la clase base.

#### Principio de Segregación de Interfaces (ISP)
- **Violación**: No se observa una violación directa de ISP en el código proporcionado.
- **Recomendación**: Revisar las interfaces y clases para asegurarnos de que no estén forzando a las clases a depender de métodos que no utilizan.

#### Principio de Inversión de Dependencias (DIP)
- **Violación**: La clase `FerryAvailabilityService` depende directamente de la implementación de `TimeTables` y `PortManager`.
  La clase `TimeTableService` depende directamente de la implementación de `TimeTables`, `Bookings`, y `FerryAvailabilityService`.

- **Recomendación**: Introducir interfaces para `TimeTables`, `Bookings` y `PortManager` y asegurarnos de que `FerryAvailabilityService`
  dependa de estas interfaces.

### Otros Principios

#### Principio KISS (Keep It Simple, Stupid)
- **Violación**: La clase `FerryAvailabilityService` tiene un método `nextFerryAvailableFrom` que es bastante complejo con múltiples bucles y condiciones anidadas.
  La clase `TimeTableService` tiene métodos como `getTimeTable` y `getAvailableCrossings` que son bastante complejos y manejan múltiples responsabilidades.
- **Recomendación**: Simplificar los métodos dividiéndolos en funciones más pequeñas y enfocadas.

#### Principio XP (Extreme Programming)
- **Violación**: No se observa una violación directa de los principios XP en el código proporcionado.
- **Recomendación**: Asegurarnos de seguir prácticas como la programación en parejas, pruebas unitarias, refactorización continua, y revisión de código.

#### Principio YAGNI (You Aren't Gonna Need It)
- **Violación**: Algunas partes del código pueden estar anticipando funcionalidades futuras que podrían no ser necesarias en este momento, como `FerryManager` y `FerryModule`,
  que podrían ser sobreingeniería si no se utilizan en el presente.

#### Principio DRY (Don't Repeat Yourself)
- **Violación**: Hay repetición en el código, especialmente en la forma en que se manejan las listas de `Booking`, `TimeTableEntry` y `Port`.
- **Recomendación**: Extraer la lógica repetida a métodos separados o utilizar funciones de agregación de colecciones para evitar la repetición.



---
# Prácticas XP para Mejorar la Calidad del Código

Aplicar las siguientes prácticas de XP puede ayudar a mejorar significativamente la calidad del código en este repositorio,
haciéndolo más limpio, mantenible, escalable, testeable y comprensible.


## Test-Driven Development (TDD)
- **Aplicación**: Escribir pruebas unitarias antes de implementar alguna funcionalidad nueva.
- **Beneficio**: Asegura que el código cumpla con los requisitos antes de su implementación y facilita la detección temprana de errores.

## Pair Programming
- **Aplicación**: Trabajar en parejas en el desarrollo del código.
- **Beneficio**: Mejora la calidad del código al compartir conocimientos y asegurar que se aborden los problemas de manera colaborativa.

## Refactoring
- **Aplicación**: Revisar y mejorar el código existente, , especialmente en las clases `TimeTables`, `TimeTableService`, y cualquier otra clase que pueda beneficiarse de una estructura más clara o eficiente.
- **Beneficio**: Mejora la mantenibilidad y legibilidad del código, facilitando futuras modificaciones y mejoras.

## Continuous Integration (CI)
- **Aplicación**: Integrar el código de manera continua, ejecutando pruebas automáticas en cada commit.
- **Beneficio**: Reduce el tiempo y el esfuerzo necesarios para identificar y corregir errores.

## Small Releases
- **Aplicación**: Desarrollar y lanzar versiones pequeñas y funcionales del software de manera regular.
- **Beneficio**: Permite obtener retroalimentación temprana y ajustar el desarrollo según sea necesario.

## Simple Design
- **Aplicación**: Diseñar el software de manera simple y enfocada, evitando la complejidad innecesaria.
- **Beneficio**: Mejora la comprensión y mantenibilidad del código.

## Real Customer Involvement
- **Aplicación**:a los usuarios reales en el proceso de desarrollo, a través de pruebas de usuario, feedback, y demostraciones.
  Esto puede ayudar a asegurar que el sistema de reserva de ferries cumpla con las necesidades y expectativas de los usuarios finales.
- **Beneficio**: Asegura que el producto final esté alineado con los requisitos del cliente.

