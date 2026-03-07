# Feature Specification: Consultar Cliente

**Created**: 07-03-2026  
**Status**: In Development

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Consulta de cliente por ID Nacional (Priority: P1)

Yo como Asesor Comercial necesito consultar un cliente utilizando su ID Nacional (número de documento) para obtener su ID de base de datos. Para poder guardar la forma de pago asociada al cliente correcto en la base de datos.

**Why this priority**: Es necesario para mantener la concordancia entre módulos; el ID de BD es el identificador interno que se usa para todas las operaciones de base de datos.

**Independent Test**: Consultar un cliente por ID Nacional y verificar que el ID de BD retornado coincida con el registrado en la base de datos.

**Acceptance Scenarios**:

1. **Scenario**: Consulta exitosa de cliente por ID Nacional
    - **Given**: Existe un cliente registrado en el sistema con ID Nacional "12345678"
    - **When**: Se envía una solicitud de consulta con ese ID Nacional al Módulo de Clientes
    - **Then**: El sistema retorna el ID de base de datos del cliente (ej: "100")

2. **Scenario**: Cliente no encontrado por ID Nacional
    - **Given**: No existe ningún cliente registrado con el ID Nacional proporcionado
    - **When**: Se envía una solicitud de consulta con ese ID Nacional
    - **Then**: El sistema retorna un error indicando que el cliente no fue encontrado

---


## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: El sistema DEBE exponer un endpoint (petición GET) que reciba el parámetro `id_nacional` y retorne el ID de base de datos del cliente.
- **FR-002**: El sistema DEBE retornar un mensaje de error claro cuando el cliente no sea encontrado: "Cliente no encontrado con el ID Nacional proporcionado".
- **FR-003**: El sistema DEBE validar que el ID Nacional no esté vacío.
- **FR-004**: El sistema DEBE permitir consultar clientes independientemente de su estado (activo, inactivo).
- **FR-005**: El sistema DEBE opcionalmente retornar datos básicos del cliente: nombre, teléfono, dirección.
- **FR-006**: El sistema DEBE realizar la consulta en tiempo real sin almacenar datos en el módulo que consulta.

### Key Entities *(include if feature involves data)*

- **Cliente** (consultado del Módulo de Gestión de Clientes): 
  - [id_cliente, id_nacional, nombre, teléfono, dirección, estado]
  - Entidad que representa al cliente registrado en el sistema.

---

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: El sistema debe retornar el ID de BD del cliente en menos de 500ms después de recibir la solicitud.

- **SC-002**: El 100% de las consultas con ID Nacional válido de clientes existentes deben retornar el ID de BD correcto.

- **SC-003**: El sistema debe retornar un mensaje de error apropiado cuando el cliente no existe.

- **SC-004**: La consulta debe funcionar correctamente bajo carga de al menos 100 solicitudes simultáneas.

---


## Notas de Implementación

- **Consulta en tiempo real**: La consulta de cliente se realiza directamente al Módulo de Gestión de Clientes. NO se almacenan datos en la base de datos del módulo que consulta.

- **Uso principal**: Este endpoint es utilizado por el Asesor Comercial en Ingresar Forma de Pago Cliente para obtener el ID de BD del cliente antes de guardar la forma de pago.

- **ID de BD vs ID Nacional**: 
  - ID Nacional: Número de documento del cliente (cédula, etc.) - usado para buscar
  - ID de BD: Identificador interno del cliente en la base de datos - usado para relaciones
