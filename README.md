# Sistema de Gestión de Biblioteca Escolar

Este repositorio contiene el producto técnico correspondiente a la **Evaluación Final Transversal (EFT)** de la asignatura **Desarrollo Orientado a Objetos II**. La aplicación es un software de escritorio de mediana complejidad diseñado bajo el patrón arquitectónico por capas y dotado de persistencia segura de datos y procesamiento asíncrono.

---

## Arquitectura y Patrones de Diseño Implementados

Para asegurar el cumplimiento del 100% de los indicadores de la rúbrica institucional, el sistema fue estructurado bajo las siguientes pautas de ingeniería de software:

### 1. Patrón Arquitectónico MVC (Modelo - Vista - Controlador)
Separación absoluta de responsabilidades para garantizar la modularidad y el mantenimiento del sistema:
*   **Modelo (`model/`):** Clases puras de modelado de datos orientadas a objetos (`Usuario`, `Libro`, `Estudiante`, `Prestamo`).
*   **Vista (`view/`):** Interfaces gráficas interactivas desarrolladas con componentes nativos de Java Swing (`JFrame`, `JComboBox`, `JTextField`, `JButton`).
*   **Controlador / Capa de Datos (`dao/`):** Componentes encargados de interceptar los eventos visuales e interactuar de forma aislada con la persistencia.

### 2. Patrón Estructural Singleton (Conexión JDBC)
Ubicado en la clase `DatabaseConnection`, este patrón asegura mediante un método estático sincronizado `getInstance()` la existencia de una **única instancia de conexión activa hacia MySQL** durante todo el ciclo de vida del software, optimizando drásticamente los recursos de red del servidor local.

### 3. Patrón de Persistencia DAO (Data Access Object)
Las operaciones CRUD sobre el motor de base de datos relacional se gestionan de forma exclusiva en clases DAO utilizando sentencias preparadas de manera segura:
*   **`PreparedStatement`:** Implementado en todas las transacciones de inserción, lectura y actualización para mitigar vulnerabilidades e inyecciones SQL indebidas.
*   **`ResultSet`:** Utilizado para el mapeo secuencial y extracción ágil de filas desde las tablas hacia colecciones de objetos Java en memoria.

---

## Concurrencia y Control de Consistencia Sincronizada

El núcleo de la aplicación procesa los flujos críticos de la pauta mediante programación multihilo y exclusión mutua:
*   **Procesamiento Asíncrono en Segundo Plano:** El registro de préstamos y la deducción de inventario se delegan a un hilo independiente (`new Thread()`). Esta acción previene condiciones de espera y **evita por completo el congelamiento o bloqueo de la interfaz gráfica (GUI)** durante la comunicación con la base de datos.
*   **Sincronización contra Condiciones de Carrera:** El método `actualizarStock` de la capa de datos incorpora la palabra clave **`synchronized`**, actuando como un mecanismo de bloqueo que garantiza la consistencia del stock ante solicitudes simultáneas concurrentes.

---

## Estructura de Paquetes en el Proyecto

```text
src/
├── database/
│   └── DatabaseConnection.java     <-- Conexión JDBC Patrón Singleton
├── model/
│   ├── Usuario.java
│   ├── Estudiante.java
│   ├── Libro.java
│   └── Prestamo.java
├── dao/
│   ├── UsuarioDAO.java
│   ├── LibroDAO.java
│   └── PrestamoDAO.java
├── view/
│   ├── LoginView.java             <-- Pantalla de Autenticación y Control de Roles
│   └── DashboardView.java         <-- Panel Operativo Multihilo (Swing)
└── main/
    └── Main.java                  <-- Punto de entrada de la aplicación
```

---

## Puesta en Marcha y Pruebas

1.  **Base de Datos (MySQL / XAMPP):** Asegurarse de iniciar los servicios de Apache y MySQL en el panel de control de XAMPP.
2.  **Aprovisionamiento:** Crear la base de datos llamada estrictamente `biblioteca` e importar secuencialmente los scripts oficiales de creación y poblado provistos por la cátedra.
3.  **Ejecución:** Abrir el proyecto en IntelliJ, enlazar la librería del driver `mysql-connector-j` en la estructura del proyecto y ejecutar la clase principal `Main.java`.
4.  **Cuentas de Prueba para Validación de Roles:**
    *   **Rol Bibliotecario (Escritura total):** Correo: `antonia@correo.cl` | Contraseña: `clave123`
    *   **Rol Estudiante (Solo consulta / Lectura):** Correo: `carlos@correo.cl` | Contraseña: `clave123`
