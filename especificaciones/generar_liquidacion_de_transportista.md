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

### User Story 2 - Validación de datos cruzados entre módulos (Priority: P1)

Yo como Sistema Financiero necesito validar que existan los datos de entrada requeridos, valor del pedido y tasa de efectividad antes de intentar procesar el cálculo. Para evitar registros contables erróneos.

**Why this priority:** Protege la integridad de la base de datos y garantiza que el módulo financiero no falle por culpa de datos incompletos de los módulos de Logistica o de Gestión de Inventario.

**Independent Test:** Forzar el cálculo de una liquidación para un pedido que no tiene registrado su valor comercial en el sistema de inventario o un reporte del modulo de losgitica donde envien una taza de efectividad nula o por fuera de los parametros.

**Acceptance Scenarios:**

1. **Scenario:** Intento de liquidación con datos incompletos
	- **Given:** Tasa de efectividad registrada de un pedido, no se encuentra el valor del pedido registrado por el Módulo de Gestión de Inventario.
	- **When:** Cuando se desea generar la liquidación del transportista.
	- **Then:** El sistema bloquea la operación, no genera la liquidación y muestra un error de "Falta valor comercial del pedido".

2. **Scenario:** Intento de liquidación con datos incompletos
	- **Given:** Valor del pedido registrado, no se encuentra la Tasa de efectividad registrada por el Módulo de Logística.
	- **When:** Cuando se desea generar la liquidación del transportista.
	- **Then:** El sistema bloquea la operación, no genera la liquidación y muestra un error de "Falta taza de efectividad del pedido".

### Edge Cases

- ¿Qué pasa si un pedido es reportado como "Devolución (Error Empresa)"?
- El sistema calcula el 10% del pedido como tarifa base, pero al aplicar el 0% de la matriz, la liquidación a pagar al transportista es de $0, y se debe generar un reporte como perdida operativa.

- ¿Qué pasa si el cálculo del 10% del pedido arroja decimales muy largos?
- El sistema debe redondear al valor entero más cercano para evitar problemas.
  
- ¿Qué pasa si la tasa de efectividad recibida desde el Módulo de Logística está fuera del rango 0-100%?
- El sistema debe rechazar el valor, bloquear el cálculo y emitir una alerta de "Dato de efectividad inválido".

- ¿Qué pasa si un pedido cambia de estado después de haber sido liquidado (ej. el cliente hace un reclamo posterior a la entrega)?
- El sistema no debe modificar ni sobrescribir la liquidación original, ya que esto rompería la auditoría contable, en su lugar, el sistema debe requerir o permitir la generación de un nuevo registro tipo "Nota de Ajuste" o "Corrección Financiera" vinculada al mismo ID de pedido.

## Requirements (mandatory)

### Functional Requirements

- **FR-001**: El sistema MUST obtener el valor total del pedido ingresado por el Módulo de Gestión de Inventario.
- **FR-002**: El sistema MUST obtener el estado de entrega "Tasa de efectividad de la distribución del pedido" del Módulo de Logística.
- **FR-003**: El sistema MUST calcular la "tarifa base" del transportista extrayendo exactamente el 10% del valor total del pedido.
- **FR-004**: El sistema MUST aplicar a la tarifa base los porcentajes establecidos en la Matriz de Liquidación Logística (100% Entregado, 80% Rechazo Parcial, 0% Devolución empresa, -100% Faltante).
- **FR-005**: El sistema MUST bloquear y cancelar la generación de la liquidación si el valor comercial del pedido es nulo, cero, o si no hay un estado de entrega final reportado.
  
## Success Criteria (mandatory)

### Measurable Outcomes

- **SC-001**: El 100% de las liquidaciones generadas reflejan exactamente el cálculo del 10% del valor del pedido ajustado por la matriz de efectividad.
- **SC-002**: El sistema no genera ninguna liquidación (0%) con campos de pago vacíos o nulos por falta de datos de entrada.
