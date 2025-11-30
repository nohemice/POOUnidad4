# 🎥 Plataforma de Gestión de Medios Digitales (PGMD)

## 🎯 Objetivo General y Diseño del Sistema

El proyecto PGMD es una aplicación desarrollada en **Java** cuyo propósito es administrar un inventario de contenido audiovisual. El diseño se enfoca en la implementación práctica de principios de **Ingeniería de Software Avanzada**, incluyendo la arquitectura **MVC**, la aplicación estricta de las normas **SOLID**, la producción de **código limpio** y una estrategia integral de **pruebas unitarias**.

---

## I. Arquitectura y Fundamentos de Implementación

### 1. Robustez en la Persistencia de Datos (I/O)

Se estableció una capa de manejo de archivos enfocada en la fiabilidad y la gestión segura de fallos.

* **Operaciones de Archivo:** El sistema está diseñado para **serializar** el estado interno (el catálogo de contenidos) hacia el archivo `contenidos.csv` y para **deserializar** los datos desde el mismo archivo, reconstruyendo los objetos **`ContenidoAudiovisual`** y sus subtipos al inicio.
* **Tolerancia a Fallos:** Se implementó el **manejo estructurado de excepciones** (`IOException`, `FileNotFoundException`) para garantizar que el programa pueda recuperarse o cerrar de forma segura ante cualquier error de entrada o salida de datos.

### 2. Principios de Diseño Orientado a Objetos (SOLID)

La arquitectura está firmemente cimentada en los principios **SOLID**, lo que asegura un alto grado de **modularidad y flexibilidad**: 

* **Separación de Competencias (SRP):** Las responsabilidades están distribuidas de forma inequívoca: el **`ContentService`** es el dominio de la lógica, el **`ConsoleView`** maneja la interfaz de usuario, y el **`CsvFileHandler`** se dedica exclusivamente al acceso a datos.
* **Extensibilidad Controlada (OCP):** La jerarquía de herencia de **`ContenidoAudiovisual`** permite la introducción de nuevos tipos de contenido (extensión) sin requerir modificaciones en las clases ya existentes (cierre a la modificación).
* **Intercambiabilidad (LSP):** Todos los subtipos de contenido pueden ser sustituidos por la clase base en cualquier punto de la aplicación, manteniendo el comportamiento esperado gracias a la consistencia de sus contratos.
* **Inversión de Control (DIP):** El servicio principal (**`ContentService`**) opera contra la abstracción (**`IFileHandler`**), lo que le permite desentenderse de la implementación específica de la persistencia y cambiarla fácilmente si fuera necesario.

### 3. Calidad del Código y Refactorización

Se ejecutó una refactorización con el objetivo de optimizar la **mantenibilidad** y la **legibilidad**:

* **Modularidad de Salida:** Se modificó la funcionalidad de **`mostrarDetalles()`** en las clases de contenido para que su única función sea **retornar la cadena de texto formateada** del detalle, en lugar de imprimirla directamente. Esta mejora desacopla la lógica de datos de la lógica de presentación.
* **Coherencia y Claridad:** Se eliminaron parámetros superfluos de constructores (`SerieDeTV`) y se corrigieron inconsistencias en la inicialización de objetos (`Cortometraje`, `Podcast`), asegurando que la interfaz pública de las clases sea más limpia y que la creación de objetos sea predecible.

### 4. Modelo Arquitectónico (MVC)

El sistema se estructura siguiendo el patrón **Modelo-Vista-Controlador**, proveyendo una clara segmentación de las responsabilidades: 

[Image of the Model-View-Controller (MVC) components]


* **Modelo:** Las clases del paquete `uni1a` (las entidades de contenido) almacenan el estado y aplican la lógica específica del dominio.
* **Controlador:** La clase **`ContentService`** actúa como el gestor de la aplicación, recibiendo comandos de la Vista, aplicando la lógica de negocio y coordinando el acceso a datos.
* **Vista:** La clase **`ConsoleView`** se limita a la interacción con el usuario (mostrar menús y capturar *input*).

### 5. Aseguramiento de la Calidad (Testing)

Se desarrolló una suite de pruebas para verificar la fiabilidad del sistema.

* **Frameworks:** Se empleó **JUnit 5** como el *framework* principal y **Mockito** para la simulación de objetos y dependencias.
* **Aislamiento:** Esta combinación permite probar la lógica de negocio (**`ContentService`**) de forma aislada. Mediante el *mocking* de la interfaz `IFileHandler`, se garantiza que la funcionalidad clave (ej., la carga de datos) se valide sin depender de las operaciones reales del sistema de archivos.

---

## II. Guía de Despliegue y Pruebas

### ⚙️ Requerimientos del Entorno

* **JDK:** Versión 16 o posterior.
* **IDE:** Se recomienda usar un entorno de desarrollo como IntelliJ IDEA.

### 🚀 Instrucciones de Arranque

1.  **Clonación:** Obtenga el código fuente desde el repositorio:
    ```bash
    git clone <URL_DEL_REPOSITORIO>
    ```
2.  **Configuración del IDE:** Abra el directorio del proyecto en IntelliJ IDEA y verifique que el **SDK del proyecto** esté configurado a una versión compatible de Java.
3.  **Ejecución:** Localice el punto de entrada, **`MainController.java`**, y ejecute el método `main()` para iniciar la aplicación de consola.

### ✅ Procedimiento de Pruebas Unitarias

1.  **Validar Dependencias:** Asegúrese de que las librerías necesarias para el *testing* (`junit-jupiter-api`, `mockito-core`, etc.) estén correctamente referenciadas en la configuración del proyecto (classpath).
2.  **Lanzamiento:** Navegue a la clase **`ContentServiceTest.java`** dentro de la estructura de carpetas de pruebas (`test`).
3.  **Ejecutar:** Use el comando "Run" o el icono de "Play" de su IDE para ejecutar la suite completa y validar el comportamiento de la lógica de negocio.
