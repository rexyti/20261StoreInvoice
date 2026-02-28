# Feature Specification: [Ingresar tasa de efectividad de la distribucion del pedido]

**Created**: 21-02-2026  
//Incompleta
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

**Independent Test**: Verificar que reciba todas las tasas de efectividad de manera correcta y las guarde en la base de datos.

**Acceptance Scenarios**: En el sistema quede guardada la tasa de efectividad correspondiente al pedido

1. **Scenario**: Etapa logistica finalizada
   - **Given**: El pedido tenga uno de los siguientes estados: cancelado, entregado o daño parcial 
   - **When**: El transportista entregue o reporte un estado que no sea "entregando"
   - **Then**: El transportista entrega la tasa de efectividad de la distribucion del pedido y esta sea recibida y guardada en la base de datos


---

### User Story 2 -  (Priority: P2)

Yo como contador dueño del software quiero que me calcule la liquidacion del transportista automaticamente al ingresar la tasa de efectividad

**Why this priority**: Porque con esta generamos la liquidacion del contratista.

**Independent Test**: verificar con el contador que la liquidacion sea correcta y coherente con el pedido

**Acceptance Scenarios**: Que genere una liquidacion correcta y mostrando con transparencia la informacion.



1. **Scenario**: La tasa de efectividad fue registrada
   - **Given**: El transportista entrega el pedido
   - **When**: El pedido este en una etapa: "devuelto, cancelado o entregado"
   - **Then**: El sistema calcula el monto correspondiente al transportista


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
- ¿Qué pasa si se envían datos incompletos del modulo 2?
  → El sistema debe devolver un error 422.


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
- **FR-006**: El sistema MUST generar historial de liquidaciones por transportista.

*Example of marking unclear requirements:*

FR-08: El sistema MUST autenticar a los usuarios antes de permitir la consulta de liquidaciones.
[NEEDS CLARIFICATION: método de autenticación no definido – ¿usuario/contraseña institucional?, ¿JWT entre módulos?, ¿SSO empresarial?]

FR-09: El sistema MUST conservar el historial de liquidaciones generadas.
[NEEDS CLARIFICATION: no se ha definido el tiempo de retención legal de los registros.]

FR-10: El sistema MUST aplicar penalizaciones según la tasa de efectividad.
[NEEDS CLARIFICATION: no se ha definido la fórmula exacta de penalización.]

FR-11: El sistema MUST recibir información del módulo de Logística mediante integración automática.
[NEEDS CLARIFICATION: no se ha definido si será REST API, mensajería asíncrona o carga manual.]

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

SC-001: El 100% de las tasas de efectividad recibidas del módulo de Logística deben almacenarse correctamente en la base de datos sin pérdida de información.

SC-002: El sistema debe generar una liquidación en menos de 3 segundos después de recibir la tasa de efectividad.

SC-003: Al menos el 95% de las consultas por ID nacional deben devolver resultados correctos en el primer intento.

SC-004: Reducir errores manuales en el cálculo de pago al transportista en un 80% respecto al proceso anterior manual.

SC-005: El sistema debe procesar al menos 500 liquidaciones diarias sin fallos críticos.

