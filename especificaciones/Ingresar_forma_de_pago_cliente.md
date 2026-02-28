# Feature Specification - Ingresar forma de pago del cliente
//Terminada
**Created:** 24-02-2026
## User Scenarios & Testing (mandatory)

### User Story 1 - Asignación de modalidad de pago a cliente nuevo o existente (Priority: P1)

Yo como Asesor Comercial necesito registrar la forma de pago (Contra Entrega o Cartera Comercial) que tendrá un cliente. Para establecer las reglas de recaudo antes de generar pedidos.

**Why this priority:** Define si el conductor recibe el efectivo o se tiene en cuenta para credito  
**Independent Test**: Seleccionar un cliente en la base de datos y asignarle "Cartera Comercial", verificando que la actualización sea exitosa.

**Acceptance Scenarios:**
1. Scenario: Asignación exitosa a cliente existente
	- **Given:** Un cliente previamente registrado en el sistema
	- **When:** El asesor selecciona la opción Pago Contra Entrega y guarda los cambios
	- **Then:** El perfil del cliente se actualiza con la nueva forma de pago
2. Scenario: Cliente no registrado
	- **Given**: Un intento de asignar forma de pago
	- **When:** El asesor busca un ID Nacional y no existe en la base de datos
	- **Then:** El sistema solicita ejecutar Registrar cliente antes de continuar

### Edge Cases

- El asesor ingresa caracteres no numéricos o espacios en blanco en el campo de búsqueda de ID Nacional?
- El sistema debe mostrar un mensaje de error en lugar de fallar.

## Requirements (mandatory)

### Functional Requirements

- **FR-001**: System MUST permitir la selección entre al menos dos formas de pago: Pago Contra Entrega y Cartera Comercial.
- **FR-002:** System MUST requerir que el cliente exista en la base de datos antes de guardar la forma de pago.

## Success Criteria (mandatory)

### Measurable Outcomes

- **SC-001:** Todos los clientes en la base de datos deben tener una forma de pago obligatoria asociada para poder generarles un pedido.
- **SC-002:** El cambio de forma de pago debe actualizarse en tiempo real para no afectar los pedidos que se generen.
