# Feature Specification: [Ingresar tasa de efectividad de la distribucion del pedido]

**Created**: 21-02-2026  

## User Scenarios & Testing *(mandatory)*

<!--
  IMPORTANT: User stories should be PRIORITIZED as user journeys ordered by importance.
  Each user story/journey must be INDEPENDENTLY TESTABLE - meaning if you implement just ONE of them,
  you should still have a viable MVP (Minimum Viable Product) that delivers value.
  
  Assign priorities (P1, P2, P3, etc.) to each story, where P1 is the most critical.
  Think of each story as a standalone slice of functionality that can be:
  - Developed independently
  - Tested independently
  - Deployed independently
  - Demonstrated to users independently
-->

### User Story 1 - [] (Priority: P1)

Yo como desarrollador necesito que la tasa de efectividad de la distribucion del pedido se ingrese en la base de datos. Para que pueda ser consultada por el sistema y generar una liquidacion correspondiente.

**Why this priority**: Porque permite finalizar el flujo de ejecucion.

**Independent Test**: Que reciba todas las tasas de efectividad de manera correcta y las guarde en la base de datos.

**Acceptance Scenarios**:

1. **Scenario**: Etapa logistica finalizada
   - **Given**: El pedido tenga uno de los siguientes estados: cancelado, entregado o daño parcial 
   - **When**: El transportista entregue o reporte un estado que no sea "entregando"
   - **Then**: El transportista entrega la tasa de efectividad de la distribucion del pedido y esta sea recibida y guardada en la base de datos


2. **Scenario**: 
   - **Given**: 
   - **When**: 
   - **Then**: 
   
---

### User Story 2 -  (Priority: P2)

Yo como contador quiero consultar liquidaciones de un usuario en especifico (se usa ID nacional). Para consultar informacion especifica con
fines legales

**Why this priority**: 

**Independent Test**: 

**Acceptance Scenarios**:

1. **Scenario**: 
   - **Given**: 
   - **When**: 
   - **Then**: 


---

### Edge Cases

<!--
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right edge cases.
-->

- ¿Qué pasa si el módulo 2 no envía la información?
  → El sistema debe registrar error y no permitir liquidación.

- ¿Qué pasa si la tasa es mayor a 100% o menor a 0%?
  → El sistema debe rechazar el valor.

- ¿Qué pasa si un pedido cambia de estado después de liquidado?
  → Debe generarse ajuste o nota de corrección.


## Requirements *(mandatory)*

<!--
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right functional requirements.
-->

### Functional Requirements

### Functional Requirements

- **FR-001**: El sistema MUST recibir del módulo de Logística el estado final de cada pedido.
- **FR-002**: El sistema MUST almacenar la tasa de efectividad asociada al pedido.
- **FR-003**: El sistema MUST validar que la tasa esté entre 0% y 100%.
- **FR-004**: El sistema MUST calcular automáticamente el monto a pagar al transportista.
- **FR-005**: El sistema MUST permitir consultar liquidaciones por ID nacional.
- **FR-006**: El sistema MUST generar historial de liquidaciones por transportista.
- **FR-007**: El sistema MUST bloquear liquidaciones si faltan datos del módulo 2.

*Example of marking unclear requirements:*

- **FR-006**: System MUST authenticate users via [NEEDS CLARIFICATION: auth method not specified - email/password, SSO, OAuth?]
- **FR-007**: System MUST retain user data for [NEEDS CLARIFICATION: retention period not specified]

### Key Entities *(include if feature involves data)*

- **Transportista**:
  Representa al responsable de la entrega.
  Atributos clave: ID nacional, nombre, contrato, tarifa base.

- **Pedido**:
  Representa una orden despachada.
  Atributos clave: ID pedido, estado final, valor del pedido, transportista asignado.

- **Liquidación**:
  Representa el balance de pago.
  Atributos clave: ID liquidación, tasa de efectividad, monto calculado, fecha, transportista asociado.

- **Tasa de Efectividad**:
  Representa el porcentaje de cumplimiento del despacho.
  Atributos clave: porcentaje, fecha de cálculo, pedido asociado.

## Success Criteria *(mandatory)*

<!--
  ACTION REQUIRED: Define measurable success criteria.
  These must be technology-agnostic and measurable.
-->

### Measurable Outcomes

- **SC-001**: [Measurable metric, e.g., "Users can complete account creation in under 2 minutes"]
- **SC-002**: [Measurable metric, e.g., "System handles 1000 concurrent users without degradation"]
- **SC-003**: [User satisfaction metric, e.g., "90% of users successfully complete primary task on first attempt"]
- **SC-004**: [Business metric, e.g., "Reduce support tickets related to [X] by 50%"]

