# Feature Specification: [Registrar cliente]
**Created**: 21-02-2026  

## User Scenarios & Testing

### User Story 1 - Registro de Nuevo Cliente (Priority: P1)
**Yo como** asesor comercial **debo** registrar clientes en el sistema, **con el objetivo de** vincularlos posteriormente a facturas y procesos de liquidación.

* **Why this priority**: Es la entidad base para la facturación y trazabilidad legal.
* **Independent Test**: Intentar guardar un cliente con todos los campos obligatorios y verificar que se genere un registro único en la base de datos.
* **Acceptance Scenarios**:
    1.  **Scenario: Registro exitoso**
        * **Given**: Un ID Nacional que no existe previamente para ese tipo de documento.
        * **When**: El asesor completa ID Nacional, Nombre, Apellido, Dirección y Teléfono y presiona "Guardar".
        * **Then**: El sistema genera un ID interno único, confirma el registro y los datos persisten en la BD.
    2.  **Scenario: ID Nacional Duplicado**
        * **Given**: Un cliente ya registrado con el ID Nacional "12345".
        * **When**: Se intenta registrar un nuevo cliente con el mismo ID Nacional "12345".
        * **Then**: El sistema bloquea la acción y muestra el mensaje: "Error: El número de identificación ya se encuentra registrado".

### User Story 2 - Modificación de datos (Priority: P2)
**Yo como** asesor comercial **quiero** actualizar la información de un cliente existente **para** mantener la base de datos al día.

* **Acceptance Scenario**:
    1.  **Scenario: Actualización de datos de contacto**
        * **Given**: Un cliente seleccionado de la lista (identificado por su ID interno).
        * **When**: Se modifica el campo "Teléfono" o "Dirección" y se guarda.
        * **Then**: El sistema actualiza los valores y mantiene la integridad del registro original.

---

### Edge Cases
* **Registro Duplicado Internacional**: ¿Qué pasa si se intenta registrar un ID Nacional que ya existe en el mismo País?
  → El sistema debe bloquear el registro, ya que la combinación {Pais_Origen + ID_Nacional} debe ser única. Debe mostrar: "Este documento ya está registrado para el país seleccionado".
* **Campos Vacíos**: El sistema debe impedir el guardado si faltan campos obligatorios, resaltando el error en la interfaz.
* **ID Nacional con Formato Internacional**: El sistema debe permitir caracteres alfanuméricos en el ID Nacional para soportar pasaportes o identificaciones de otros países.
* **Búsqueda Fallida**: Si se busca un cliente que no existe, el sistema debe ofrecer la opción de "Registrar nuevo cliente" inmediatamente.


---

## Requirements

### Functional Requirements
* **FR-001**: El sistema **MUST** generar automáticamente un ID único secuencial para cada cliente, el cual funcionará como Clave Primaria interna.
* **FR-002**: El sistema **MUST** validar que la combinación de "País + ID Nacional" sea única para evitar duplicidades en un contexto internacional.
* **FR-003**: El sistema **MUST** obligar el llenado de los campos: ID Nacional, Nombre, Apellido y Teléfono.
* **FR-004**: El sistema **MUST** permitir la edición de la información del cliente, incluyendo el ID Nacional (en caso de error de digitación), siempre que no colisione con otro registro existente.
* **FR-005**: El sistema **MUST** registrar un log de auditoría que guarde: Usuario que realizó la acción, Fecha/Hora y Tipo de operación (Creación/Edición).

### Key Entities
* **[Cliente]**:
    * `ID_Cliente` (PK - Artificial, Numérico o UUID)
    * `ID_Nacional` (String - Atributo de identidad)
    * `Pais_Origen`(String o ID si esta en otra tabla para relacion)
    * `Nombre` (String)
    * `Apellido` (String)
    * `Dirección` (String)
    * `Teléfono` (String)
    * `Fecha_Registro` (Timestamp)

---

## Success Criteria

### Measurable Outcomes
* **SC-001**: El tiempo de registro de un cliente (desde el clic en guardar hasta la confirmación) debe ser menor a **2 segundos**.
* **SC-002**: El sistema debe soportar identificaciones de hasta 20 caracteres alfanuméricos para cubrir estándares internacionales.
* **SC-003**: El 100% de las modificaciones deben quedar registradas en la tabla de auditoría para fines legales.
* **SC-004**: La interfaz de registro debe cargar en menos de **1 segundo** tras la petición del asesor comercial.
