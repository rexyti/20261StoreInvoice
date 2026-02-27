# Feature Specification - Generar Liquidación de transportista

**Created:** 24-02-2026
## User Scenarios & Testing (mandatory)

### User Story 1 - Cálculo automatizado de liquidación según efectividad y valor (Priority: P1)

Yo como Sistema Financiero (Módulo 3) necesito calcular el monto a pagar al transportista tomando como base el 10% del valor total del pedido y aplicando la taza de efectividad una vez que el Módulo de Logística reporte el estado final. Esto con el objetivo de asegurar el pago a los aliados de transporte.

**Why this priority:**  Es una función importante del módulo financiera; sin esto, no se puede pagar a los transportistas.
**Independent Test**: Añadir en la base de datos pedidos con diferentes valores y distintas tasas de efectividad, verificando que el cálculo final sea exacto según la regla de pago a los transportistas.

**Acceptance Scenarios:** 

1. Scenario: Liquidación por Estado de Entrega Exitosa (100% de la tarifa)
	- **Given:** El Módulo de Gestión de Inventario ha registrado un pedido con un valor, el Módulo de Logística ha registrado un estado de "Entregado Completo" para ese pedido.
	- **When:** Cuando se desea generar la liquidación del transportista
	- **Then:** El sistema calcula la tarifa según el pedido, aplica el 100% de pago por la taza de efetividad, y genera una liquidación a pagar.

2. Scenario: Liquidación con Rechazo Parcial (80% de la tarifa)
	- **Given**: El Módulo de Gestión de Inventario ha registrado un pedido con un valor, el Módulo de Logística ha registrado un estado de "Rechazo parcial" para ese pedido.
	- **When:** Cuando se desea generar la liquidación del transportista
	- **Then:** El sistema calcula la tarifa según el pedido, aplica el 80% de pago por la taza de efetividad, y genera una liquidación a pagar.

3. Scenario: Penalización por Faltante de Inventario (Pérdida)
	- **Given**: El Módulo de Gestión de Inventario ha registrado un pedido con un valor, el Módulo de Logística ha registrado un estado de "Faltante de Inventario" para ese pedido.
	- **When:** Cuando se desea generar la liquidación del transportista
	- **Then:** El sistema calcula la tarifa según el pedido, aplica el -100% de pago por la taza de efetividad, y genera una liquidación en contra del transportista.

4. Scenario: Penalización por Faltante de Inventario (Pérdida)
	- **Given**: El Módulo de Gestión de Inventario ha registrado un pedido con un valor, el Módulo de Logística ha registrado un estado de "Faltante de Inventario" para ese pedido.
	- **When:** Cuando se desea generar la liquidación del transportista
	- **Then:** El sistema calcula la tarifa según el pedido, aplica el 0% de pago por la taza de efetividad, y genera una liquidación, y registra el costo del flete como una pérdida operativa.

### User Story 2 -  (Priority: P2)

-----------------

**Why this priority:** 

**Independent Test:** Asignar "Cartera Comercial" 

**Acceptance Scenarios:**
1. **Scenario:** 
	- **Given:** 
	- **When:** 
	- **Then:** 

### Edge Cases

- What happens when ?
- How does system handle ?

## Requirements (mandatory)

### Functional Requirements

- **FR-001**: System MUST 
- **FR-002:** System MUST 
- **FR-003:** System MUST
  
## Success Criteria (mandatory)

### Measurable Outcomes

- **SC-001:** 
- **SC-002:** 
