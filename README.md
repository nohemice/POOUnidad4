# 📝 Documento de Síntesis Técnica: Gestión de Contenido Audiovisual

## 1. Gestión de Persistencia: Mecanismos de I/O de Alta Fidelidad

La funcionalidad de almacenamiento de datos se diseñó mediante una arquitectura de **desacoplamiento total**, fundamentada en contratos de abstracción para garantizar la intercambiabilidad tecnológica.

* **Contrato de Persistencia:** El sistema utiliza la interfaz **`IFileHandler`** para definir las responsabilidades de serialización/deserialización. La clase **`CsvFileHandler`** se encarga de la materialización de estos contratos, interactuando específicamente con el repositorio `contenidos.csv`. Esta dependencia de la interfaz facilita la **sustitución de la fuente de datos** sin impacto en el núcleo de la lógica de negocio.
* **Aseguramiento de Recursos:** Para mitigar el riesgo de **filtración de recursos** del sistema operativo, las operaciones de entrada/salida (`loadContents` y `saveContents`) están envueltas en la construcción **`try-with-resources`**. Esta disciplina técnica asegura la clausura automática y eficiente de los flujos (`BufferedReader`, `BufferedWriter`), garantizando la **integridad operativa** y la eficiencia de la memoria.
* **Provisión de Infraestructura:** El *framework* incluye una validación previa a la escritura que comprueba la existencia del *path* de datos (`data`). En caso de ausencia, la aplicación ejecuta la **creación programática y transparente** del directorio. Esta característica infunde al sistema una **capacidad de autoinicialización**, haciendo la aplicación altamente resiliente a variaciones del entorno de ejecución.
* **Mapeo Polimórfico (Parsing):** La función de lectura ejecuta la **deserialización de objetos** mediante la tokenización de registros (`String.split(",")`). Un flujo condicional subsiguiente evalúa el identificador de tipo para invocar el constructor adecuado del subtipo **`ContenidoAudiovisual`**, manteniendo así la consistencia y la integridad de la **jerarquía de herencia** durante la instanciación.

---

## 2. Optimización del Código Base: Cohesión y Legibilidad

La fase de refactorización se centró en elevar los estándares de la calidad del código, enfocándose en la **cohesión interna** de las clases y la **claridad de las interfaces**.

* **Redefinición de Responsabilidad Funcional:** El método **`mostrarDetalles()`** fue reestructurado para alinearlo con el Principio de Responsabilidad Única (**SRP**). Su nueva función es generar y **devolver la representación textual** del objeto, eliminando la impresión directa a la consola. Este cambio desacopla la **generación del dato** de su **presentación**, confiriendo mayor versatilidad y capacidad de prueba a la función.
* **Normalización de Inicialización:** Se corrigieron anomalías lógicas y fallas de inicialización en las firmas de los **constructores** (`Cortometraje`, `Podcast`, etc.), garantizando que toda instancia se cree en un **estado válido y completo**. Asimismo, se eliminó un argumento **superfluo** en la clase `SerieDeTV`, simplificando su API.
* **Eficiencia en el Manejo de Cadenas:** La construcción de mensajes de salida utiliza la clase **`StringBuilder`** en lugar de la sobrecarga del operador de concatenación (`+`). Esta es una optimización crucial que **minimiza la sobrecarga** del *Garbage Collector* al reducir la creación de objetos `String` efímeros, lo que se traduce en una mejora tangible del **rendimiento y la gestión de memoria** del sistema.

---

## 3. Principios SOLID: Estabilidad Arquitectónica y Flexibilidad

El diseño estructural está rigurosamente gobernado por los principios **S.O.L.I.D.**, asegurando un marco de trabajo maleable y de fácil mantenimiento. 

* **Cohesión Funcional (SRP):** La segregación de responsabilidades es patente. El servicio (`ContentService`) se concentra en la lógica de negocio, la vista (`ConsoleView`) en la interacción con el usuario, y el gestor de archivos (`CsvFileHandler`) en la capa I/O.
* **Diseño para la Extensión (OCP):** La jerarquía de `ContenidoAudiovisual` está configurada para ser ampliable. La introducción de nuevos tipos de contenido se efectúa mediante la **extensión** de la clase base, preservando la estabilidad del *core* del sistema (evitando modificaciones).
* **Consistencia de Contrato (LSP):** La garantía de **sustitución** se mantiene a través de la implementación uniforme de `mostrarDetalles()` en todos los subtipos, asegurando un manejo polimórfico sin introducir efectos colaterales inesperados.
* **Abstracción de Dependencia (DIP):** El módulo de alto nivel (`ContentService`) se relaciona con la abstracción (`IFileHandler`) y no con la implementación concreta, asegurando la **inversión de la dependencia** y facilitando la migración tecnológica.

---

## 4. Patrón de Diseño MVC: Arquitectura de Tres Capas Desacopladas

La aplicación sigue el patrón **Modelo-Vista-Controlador (MVC)**, estableciendo un marco de trabajo donde cada componente tiene una jurisdicción bien definida, lo que potencia la **modularidad** y la **trazabilidad**. 

### 🔹 Módulo de Datos (El Modelo)

El **Modelo** abarca las entidades y las reglas del dominio (`ContenidoAudiovisual` y derivados). Su única responsabilidad es **gestionar el estado de los datos** y su coherencia, siendo completamente ajeno a los detalles de la presentación o el control del flujo.

### 🔹 Módulo de Orquestación (El Controlador)

El **Controlador** (`ContentService`) funge como el punto de entrada para las acciones del usuario. Su rol es **procesar las peticiones**, validar y ejecutar las transacciones de negocio, interactuar con el Modelo para modificar el estado y, finalmente, seleccionar el recurso de presentación (la Vista) para el despliegue del resultado.

### 🔹 Módulo de Interfaz (La Vista)

La **Vista** (`ConsoleView`) es el canal de comunicación con el usuario. Es responsable de la **captura de eventos de entrada** y de la **representación visual** del Modelo, basándose estrictamente en los datos que le proporciona el Controlador. La Vista no contiene lógica de negocio.

---

## 5. Pruebas Unitarias: Verificación Rigurosa de la Lógica

Se implementó una estrategia de **aseguramiento de la calidad** basada en pruebas unitarias deterministas para validar el comportamiento del código base.

* **Entorno de Verificación:** La metodología se apoya en **JUnit 5** para la orquestación de pruebas y **Mockito** para la construcción de **simulacros (mocks)**, esenciales para aislar las unidades bajo prueba.
* **Aislamiento y Determinismo:** El caso **`ContentServiceTest`** ejemplifica el uso de *mocking* para el `IFileHandler`. Al simular las dependencias externas, la prueba valida que la lógica del servicio (ej., la correcta población de datos) es **independiente** del estado real del sistema de archivos. Esto garantiza que las pruebas sean **rápidas** y **altamente deterministas**.
* **Robustez del Testing:** El uso de simulacros asegura que un cambio en la implementación de la persistencia (ej. migración a una nueva biblioteca CSV) no invalide las pruebas de la lógica de negocio, demostrando una **alta calidad y bajo acoplamiento** en la estrategia de *testing*.

---

## Conclusiones Estratégicas

El proyecto constituye un ejemplo de **excelencia en ingeniería de software**. La adopción consciente de **SOLID y MVC** ha dotado a la aplicación de una estructura que favorece la **extensibilidad** y la **mantenibilidad** a largo plazo. La validación constante mediante **pruebas unitarias robustas** confirma la fiabilidad del código central, elevando el proyecto de una simple funcionalidad a una solución de software profesionalmente diseñada.
