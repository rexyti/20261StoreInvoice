# Feature Specification - Generar Liquidación de Cliente

**Created:** 03-03-2026
**Status:** In Development

## Descripción del Flujo

El Módulo de Transporteavisará al Sistema Financiero cuando un pedido alcance un estado final de entrega. En ese momento, el Sistema Financiero recibirá:
- `id_pedido`
- `estado_final` (según la matriz de liquidación)

Con esta información, el sistema generará la liquidación asociándola al ID de BD del cliente relacionado con el pedido.

---

## User Scenarios & Testing (mandatory)

### User Story 1 - Generación de liquidación según estado final de entrega (Priority: P1)

Yo como Sistema Financiero necesito generar una liquidación para un cliente cuando el Módulo de Transporte reporte llegada a un estado final de entrega. Para registrar el monto a cobrar según la efectividad de la distribución.

**Why this priority:** Es una función crítica para la conciliación financiera; sin esto, no se puede facturar ni cobrar a los clientes.

**Independent Test:** El Módulo de Transporte envía diferentes estados finales y verificar que el cálculo del total sea correcto según la matriz.

**Acceptance Scenarios:**

1. **Scenario:** Liquidación con entrega exitosa (100% del monto)
   - **Given:** El Módulo de Transporte envía el estado "Entregado Completo" con el id_pedido
   - **When:** Se desea generar la liquidación del cliente
   - **Then:** El sistema calcula el total = (Precio Pedido + Tarifa Envío) * 100% y genera una liquidación para el cliente.

2. **Scenario:** Liquidación con rechazo parcial (80% del monto)
   - **Given:** El Módulo de Transporte envía el estado "Rechazo Parcial" con el id_pedido
   - **When:** Se desea generar la liquidación del cliente
   - **Then:** El sistema calcula el total = (Precio Pedido + Tarifa Envío) * 80%

3. **Scenario:** Liquidación con devolución por error de empresa (0% del monto)
   - **Given:** El Módulo de Transporte envía el estado "Devolución (Error Empresa)" con el id_pedido
   - **When:** Se desea generar la liquidación del cliente
   - **Then:** El sistema calcula el total = (Precio Pedido + Tarifa Envío) * 0% = 0

4. **Scenario:** Liquidación con faltante de inventario (monto variable)
   - **Given:** El Módulo de Transporte envía el estado "Faltante de Inventario" con el id_pedido
   - **When:** Se desea generar la liquidación del cliente
   - **Then:** El sistema calcula el total solo por productos efectivamente entregados

---

### User Story 2 - Validación de datos requeridos (Priority: P1)

Yo como Sistema Financiero necesito validar que existan todos los datos requeridos antes de generar la liquidación. Para evitar registros contables erróneos.

**Why this priority:** Protege la integridad de la base de datos y previene facturas incompletas.

**Independent Test:** Intentar generar liquidaciones con datos faltantes y verificar que el sistema las rechace.

**Acceptance Scenarios:**

1. **Scenario:** Intento de liquidación sin precio del pedido
   - **Given:** Se intenta generar liquidación pero al consultar el id_pedido no se obtiene el precio
   - **When:** Cuando se desea generar la liquidación del cliente
   - **Then:** El sistema bloquea la operación y muestra error: "Falta precio del pedido".

2. **Scenario:** Intento de liquidación sin forma de pago registrada
   - **Given:** Se intenta generar liquidación para un cliente sin forma de pago asignada
   - **When:** Cuando se desea generar la liquidación
   - **Then:** El sistema bloquea la operación y muestra error: "Cliente sin forma de pago registrada".

3. **Scenario:** Intento de liquidación sin estado de entrega
   - **Given:** El Módulo de Transporte no ha enviado el estado final del pedido
   - **When:** Cuando se desea generar la liquidación
   - **Then:** El sistema bloquea la operación y muestra error: "Estado de entrega no disponible".

---

### User Story 3 - Diferenciación por forma de pago (Priority: P2)

Yo como Sistema Financiero necesito registrar la forma de pago del cliente en la liquidación. Para identificar si es recaudo en efectivo o factura a crédito.

**Why this priority:** Afecta la gestión del flujo de caja.

**Independent Test:** Generar liquidaciones para clientes con diferentes formas de pago y verificar que se registre correctamente.

**Acceptance Scenarios:**

1. **Scenario:** Cliente con Pago Contra Entrega
   - **Given:** Un cliente tiene asignada la forma de pago "Pago Contra Entrega"
   - **When:** Se genera la liquidación del pedido
   - **Then:** El sistema registra la liquidación con forma de pago "Contra Entrega"

2. **Scenario:** Cliente con Cartera Comercial
   - **Given:** Un cliente tiene asignada la forma de pago "Cartera Comercial"
   - **When:** Se genera la liquidación del pedido
   - **Then:** El sistema registra la liquidación con forma de pago "Cartera Comercial"

---

### Edge Cases

- **¿Qué pasa si el precio del pedido tiene decimales muy largos?**
  - El sistema debe redondear al precio entero más cercano (Round Half Up).

- **¿Qué pasa si la tarifa de envío no está definida?**
  - El sistema debe rechazar y mostrar: "Tarifa de envío no configurada para la zona".

- **¿Qué pasa si el porcentaje de efectividad está fuera del rango 0-100%?**
  - El sistema debe rechazar y mostrar: "Porcentaje de efectividad inválido".

- **¿Qué pasa si el Módulo de Transporte envía un estado que no existe en la matriz?**
  - El sistema debe rechazar y mostrar: "Estado de entrega no reconocido".

---

## Requirements (mandatory)

### Functional Requirements

- **FR-001:** El sistema DEBE recibir del Módulo de Transporte el id_pedido y el estado_final de entrega.
- **FR-002:** El sistema DEBE consultar el precio del pedido y el ID de BD del cliente (usando el endpoint consultar_pedido del Módulo de Inventario) para obtener el valor total y saber qué cliente es.
- **FR-003:** El sistema DEBE usar el ID de BD del cliente obtenido de consultar_pedido para consultar su forma de pago (Contra Entrega o Cartera Comercial).
- **FR-004:** El sistema DEBE calcular la tarifa de envío según la zona de destino.
- **FR-005:** El sistema DEBE calcular el total a cobrar aplicando la fórmula: Total = (Precio Pedido + Tarifa Envío) * Porcentaje Efectividad según matriz.
- **FR-006:** El sistema DEBE bloquear la liquidación si falta precio del pedido, forma de pago o estado de entrega.
- **FR-007:** El sistema DEBE registrar la forma de pago en la liquidación.
- **FR-008:** El sistema DEBE mantener un registro inmutable de todas las liquidaciones generadas.

### Key Entities (include if feature involves data)

- **Liquidacion_Cliente:** 
  - [id_liquidacion, id_pedido, id_cliente, precio_pedido, tarifa_envio, total_calculado, forma_pago, estado_liquidacion, fecha_liquidacion]
  - Entidad que registra el monto a cobrar al cliente.

---

## Success Criteria (mandatory)

- **SC-001:** El 100% de las liquidaciones generadas deben reflejar correctamente el cálculo según la matriz de liquidación.

- **SC-002:** El sistema debe generar una liquidación en menos de 2 segundos después de recibir el estado final.

- **SC-003:** Todas las liquidaciones deben ser rastreables e inmutables para auditoría.

- **SC-004:** El sistema debe procesar al menos 1,000 liquidaciones diarias sin fallos críticos.

---

## Matriz de Liquidación de Cliente

| Estado Final de Entrega | % Por Cobrar |
| :--- | :--- |
| **Entregado Completo** | 100% |
| **Rechazo Parcial** | 80% |
| **Devolución (Error Empresa)** | 0% |
| **Faltante de Inventario** | Variable (solo productos entregados) |
| **No Entregado** | 0% |



