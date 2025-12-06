# Proyecto Final Integrador: Arquitectura y Desarrollo SQL - Clínica "VetSalud"

**Programa:** Maestría en Ciencia de Datos  
**Módulo:** Diseño y Gestión de Bases de Datos  
**Formato de Entrega:** Informe PDF + Archivos SQL (.sql)  

---

## Introducción al Reto

Usted ha sido designado como el Arquitecto de Datos principal de "VetSalud". Su misión es diseñar y construir la base de datos central de la clínica. El proyecto se divide en dos etapas: primero debe modelar el núcleo operativo básico y, posteriormente, implementar lógica avanzada para controlar los nuevos servicios críticos de cirugía.

---

## FASE 1: El Core Operativo (Requerimientos Base)

*Esta fase evalúa su capacidad para traducir necesidades de negocio en un modelo de datos bien estructurado.*

**Contexto del Negocio:**
> "La clínica necesita un sistema para gestionar su operación. Se requiere almacenar información de los **dueños** de las mascotas (cédula, nombre, dirección, varios teléfonos). Cada **mascota** (nombre, especie, raza, fecha de nacimiento) pertenece a un único dueño. Los **Veterinarios** (cédula, nombre, especialidad) atienden a las mascotas en **citas** (fecha, hora, motivo). En cada cita, se genera un diagnóstico y se pueden recetar varios medicamentos. La clínica también quiere llevar un registro del **historial de vacunación** de cada mascota, considerando que una mascota tiene muchas vacunas a lo largo de su vida."

**Actividades a Desarrollar:**

1.  **Diseño Conceptual (Diagrama E-R):**
    * Dibuje el diagrama usando notación Chen o Pata de Gallo.
    * **Requisito:** Debe diferenciar gráficamente las **Entidades Fuertes** (ej. Dueño) de las **Entidades Débiles** (ej. Historial Vacunación) y representar los atributos multivaluados (Teléfonos).
2.  **Diseño Lógico y Normalización:**
    * Entregue la lista de tablas resultante del mapeo.
    * **Justificación:** Escriba un párrafo explicando por qué su tabla de "Citas" y "Dueños" cumple con la **Tercera Forma Normal (3NF)**, asegurando que no existan dependencias transitivas.
3.  **Implementación SQL (DDL):**
    * Escriba el script `01_esquema_basico.sql` con los comandos `CREATE TABLE`.
    * Debe incluir claves primarias (`PK`), foráneas (`FK`) y restricciones de nulidad (`NOT NULL`).

---

## FASE 2: Lógica Avanzada y Transaccionalidad

*La clínica ha crecido y requiere controles estrictos para evitar errores humanos en la programación de cirugías. Aquí debe aplicar los conceptos de transacciones y concurrencia.*

**Nuevos Requerimientos Críticos:**

1.  **Módulo de Cirugías:**
    * Se requiere agendar cirugías que involucran: una **Mascota**, un **Veterinario Cirujano**, una **Sala de Quirófano** y un **Horario** (Fecha, Hora Inicio, Hora Fin).
2.  **El Problema del "Overbooking":**
    * Actualmente, dos recepcionistas pueden reservar la misma sala a la misma hora por error.
    * **Su Tarea:** Implementar un mecanismo en base de datos que impida esto.

**Actividades a Desarrollar:**

1.  **DDL Extendido:**
    * En un script `02_esquema_avanzado.sql`, cree las tablas necesarias para `QUIROFANO` y `PROGRAMACION_CIRUGIA`.
2.  **Procedimiento Almacenado Transaccional (ACID):**
    * Cree un archivo `03_logica_negocio.sql`.
    * Escriba un **Stored Procedure** llamado `sp_agendar_cirugia`.
    * **Lógica obligatoria:**
        * Iniciar Transacción (`BEGIN`).
        * Verificar si el quirófano elegido ya está ocupado en el rango de horas solicitado.
        * Si está ocupado: Hacer `ROLLBACK` y levantar un error (Excepción).
        * Si está libre: Hacer `INSERT` y confirmar con `COMMIT`.
        * *Objetivo:* Garantizar la **Atomicidad** y **Consistencia** de los datos.

---

## Entregables Finales

El estudiante debe entregar un archivo comprimido (.zip) con la siguiente estructura ordenada:

1.  **`/Documentacion`**:
    * `Informe_Diseno.pdf`: Contiene el Diagrama E-R (imagen exportada), el esquema relacional y la justificación de la 3NF.
2.  **`/SQL`**:
    * `01_esquema_basico.sql`: Creación de tablas fase 1.
    * `02_esquema_avanzado.sql`: Creación de tablas fase 2.
    * `03_logica_negocio.sql`: Procedimiento almacenado (`sp_agendar_cirugia`).
    * `04_datos_prueba.sql`: (Opcional) Inserts de ejemplo para probar que el sistema funciona.

---

## Criterios de Evaluación

| Criterio | Descripción | Peso |
| :--- | :--- | :---: |
| **Modelado Conceptual** | Correcta identificación de entidades débiles, relaciones N:M y atributos especiales según el texto guía. | **30%** |
| **Integridad y Normalización** | Esquema en 3NF. Correcta definición de PKs y FKs en SQL. | **20%** |
| **Lógica Transaccional (ACID)** | El procedimiento almacenado funciona correctamente: permite agendar en huecos libres y **bloquea/falla** si se intenta solapar horarios. | **40%** |
| **Calidad del Código** | Scripts ordenados, sintaxis correcta y uso de convenciones de nombres. | **10%** |

---

### Consejos para el estudiante:
* **Sobre la Normalización:** Revise la Sección 7 del material. Recuerde que separar los teléfonos del dueño en una tabla aparte es una forma de cumplir la 1NF (Atomicidad).
* **Sobre Transacciones:** Para el procedimiento de cirugía, recuerde que una transacción debe ser "Todo o Nada" (Atomicidad). Si detecta un conflicto de horario, ninguna inserción debe guardarse.