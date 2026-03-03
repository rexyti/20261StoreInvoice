# Feature Specification - Generar Liquidación de Cliente

**Created:** 03-03-2026
**Status:** In Development

## User Scenarios & Testing (mandatory)

### User Story 1 - Cálculo automatizado de liquidación del cliente según pedido y envío (Priority: P1)

Yo como Sistema Financiero (Módulo 3) necesito calcular el monto total a cobrar al cliente sumando el precio del pedido más la tarifa de envío, según la tasa de efectividad de la distribución. Esto con el objetivo de garantizar el cobro correcto y a tiempo de cada venta.

**Why this priority:** Es una función crítica para la conciliación financiera; sin esto, no se puede facturar ni cobrar a los clientes.

**Independent Test:** Crear pedidos con diferentes precios, aplicar diferentes tasas de efectividad, y verificar que el cálculo final del total por cobrar sea exacto.

**Acceptance Scenarios:**

1. **Scenario:** Liquidación con entrega exitosa (100% del monto)
   - **Given:** El sistema obtiene exitosamente el precio del pedido consultando al Módulo de Gestión de Inventario, el Módulo de Logística ha registrado un estado de "Entregado Completo" para ese pedido, y se conoce la forma de pago del cliente.
   - **When:** Se desea generar la liquidación del cliente
   - **Then:** El sistema calcula el total = (Precio Pedido + Tarifa Envío) * 100% y genera una liquidación a cobrar completa.

2. **Scenario:** Liquidación con rechazo parcial (80% del monto)
   - **Given:** El sistema obtiene exitosamente el precio del pedido, el Módulo de Logística ha registrado un estado de "Rechazo Parcial" (cliente rechaza parte de los productos).
   - **When:** Se desea generar la liquidación del cliente
   - **Then:** El sistema calcula el total por los productos entregados = (Precio Productos Entregados + Tarifa Envío) * 80% y genera una liquidación con nota crédito por los productos rechazados.

3. **Scenario:** Liquidación con devolución por error de empresa (0% del pedido)
   - **Given:** El Módulo de Logística ha registrado el estado "Devolución (Error Empresa)", el cliente devuelve todos los productos.
   - **When:** Se desea generar la liquidación del cliente
   - **Then:** El sistema genera una liquidación con saldo $0 a cobrar y registra una nota crédito por el valor total del pedido (Precio Pedido + Tarifa Envío).

4. **Scenario:** Liquidación con faltante de inventario (reajuste de monto)
   - **Given:** El Módulo de Logística ha registrado un estado de "Faltante de Inventario", el cliente recibe parcialmente el pedido.
   - **When:** Se desea generar la liquidación del cliente
   - **Then:** El sistema calcula el total solo por productos efectivamente entregados y genera nota crédito por los productos faltantes.

### User Story 2 - Validación de datos cruzados entre módulos (Priority: P1)

Yo como Sistema Financiero necesito validar que existan todos los datos requeridos (precio del pedido, forma de pago, estado de entrega, datos del cliente) antes de generar la liquidación. Para evitar registros contables erróneos y garantizar la integridad de la contabilidad.

**Why this priority:** Protege la integridad de la base de datos y previene facturas incompletas o incorrectas.

**Independent Test:** Intentar generar liquidaciones con datos faltantes en diferentes módulos y verificar que el sistema las rechace.

**Acceptance Scenarios:**

1. **Scenario:** Intento de liquidación sin precio del pedido
   - **Given:** Se intenta generar liquidación de un pedido. Al consultar el Módulo de Gestión de Inventario, este no retorna el precio del pedido.
   - **When:** Cuando se desea generar la liquidación del cliente
   - **Then:** El sistema bloquea la operación y muestra error: "Falta precio del pedido".

2. **Scenario:** Intento de liquidación sin forma de pago registrada
   - **Given:** Se intenta generar liquidación para un cliente sin forma de pago (Contra Entrega o Cartera Comercial) previamente asignada.
   - **When:** Cuando se desea generar la liquidación
   - **Then:** El sistema bloquea la operación y muestra error: "Cliente sin forma de pago registrada".

3. **Scenario:** Intento de liquidación sin estado de entrega
   - **Given:** Se intenta generar liquidación de un pedido que aún no tiene reportado el estado final del Módulo de Logística.
   - **When:** Cuando se desea generar la liquidación
   - **Then:** El sistema bloquea la operación y muestra error: "Estado de entrega no disponible".

4. **Scenario:** Intento de liquidación sin tasa de efectividad
   - **Given:** Se intenta generar liquidación de un pedido sin que el Módulo de Logística haya registrado la tasa de efectividad de la distribución.
   - **When:** Cuando se desea generar la liquidación
   - **Then:** El sistema bloquea la operación y muestra error: "Falta tasa de efectividad del pedido".

### User Story 3 - Tratamiento diferenciado según forma de pago del cliente (Priority: P2)

Yo como Asesor Comercial necesito que el sistema genere liquidaciones diferenciadas según si el cliente paga Contra Entrega o en Cartera Comercial. Para gestionar correctamente la tesorería y los plazos de pago.

**Why this priority:** Afecta la gestión de flujo de caja y los términos comerciales del cliente.

**Independent Test:** Crear dos clientes idénticos pero con diferentes formas de pago, generar el mismo pedido para ambos, y verificar que las liquidaciones se generen de forma diferenciada.

**Acceptance Scenarios:**

1. **Scenario:** Cliente con Pago Contra Entrega
   - **Given:** Un cliente tiene asignada la forma de pago "Pago Contra Entrega", realiza un pedido de $500 más $80 de envío.
   - **When:** El transportista entrega el pedido exitosamente
   - **Then:** El sistema genera una liquidación por $580 marcada como "Pendiente de Recaudo en Entrega", y registra el recaudo una vez confirmado por el Módulo de Logística.

2. **Scenario:** Cliente con Cartera Comercial
   - **Given:** Un cliente tiene asignada la forma de pago "Cartera Comercial", realiza un pedido de $500 más $80 de envío.
   - **When:** El transportista entrega el pedido exitosamente
   - **Then:** El sistema genera una liquidación por $580 marcada como "Factura en Crédito", con plazo comercial a definir, sin marcar como pagada hasta que no se reciba el comprobante de pago.

### Edge Cases

- **¿Qué pasa si el precio del pedido tiene decimales muy largos o es un número con muchos dígitos?**
  - El sistema debe redondear al precio entero más cercano siguiendo el estándar de redondeo bancario (Round Half Up) para evitar problemas de precisión.

- **¿Qué pasa si la tarifa de envío no está definida en el sistema?**
  - El sistema debe rechazar el cálculo y generar una alerta: "Tarifa de envío no configurada para la zona de destino".

- **¿Qué pasa si la tasa de efectividad recibida está fuera del rango 0-100%?**
  - El sistema debe rechazar el valor, bloquear el cálculo y emitir una alerta: "Dato de efectividad inválido".

- **¿Qué pasa si un cliente paga Contra Entrega pero el transportista reporta "No Entregado"?**
  - El sistema debe generar una liquidación pendiente de recaudo marcada como "En Riesgo", y esperar confirmación de reintento de entrega.

- **¿Qué pasa si un pedido tiene multiple productos con diferentes estados de entrega (algunos entregados, otros rechazados)?**
  - El sistema debe calcular el total solo por los productos efectivamente entregados al cliente y generar notas crédito por los items no entregados.

- **¿Qué pasa si un pedido cambia de estado después de haber sido liquidado (ej: cliente reclama después de la entrega)?**
  - El sistema NO debe modificar ni sobrescribir la liquidación original, en su lugar debe requerir la generación de un nuevo registro tipo "Nota de Ajuste" o "Nota Crédito" vinculada al mismo ID de pedido para mantener la auditoría contable.

- **¿Qué pasa si un cliente con Cartera Comercial no paga en el plazo establecido?**
  - El sistema debe generar reportes de cartera vencida y alertas al contador, pero NO debe bloquear la generación de nuevos pedidos para ese cliente (a menos que exista una política de crédito específica).

## Requirements (mandatory)

### Functional Requirements

- **FR-001:** El sistema MUST consultar un endpoint (petición GET) expuesto por el Módulo de Gestión de Inventario, utilizando el id_pedido como parámetro, para obtener el precio total del pedido antes de generar la liquidación.
- **FR-002:** El sistema MUST consultar el Módulo de Gestión de Clientes para obtener la forma de pago asignada al cliente (Contra Entrega o Cartera Comercial).
- **FR-003:** El sistema MUST obtener del Módulo de Logística la tasa de efectividad de la distribución y el estado final de entrega del pedido.
- **FR-004:** El sistema MUST calcular la tarifa base de envío según la zona de destino y el peso total del pedido.
- **FR-005:** El sistema MUST calcular el total a cobrar aplicando la fórmula: Total = (Precio Pedido + Tarifa Envío) * Porcentaje Efectividad según matriz.
- **FR-006:** El sistema MUST bloquear y cancelar la generación de la liquidación si falta cualquiera de los datos requeridos (precio, forma de pago, estado de entrega, tasa de efectividad).
- **FR-007:** El sistema MUST diferenciar entre liquidaciones "Contra Entrega" (pendiente de recaudo) y "Cartera Comercial" (factura en crédito).
- **FR-008:** El sistema MUST generar automáticamente notas crédito cuando hay rechazo parcial o devolución de productos.
- **FR-009:** El sistema MUST mantener un registro de auditoría inmutable de todas las liquidaciones generadas, incluyendo emisión, modificações y anulaciones.
- **FR-010:** El sistema MUST permitir la búsqueda de liquidaciones por: ID de cliente, ID de pedido, rango de fechas, y estado de pago.

### Key Entities (include if feature involves data)

- **Cliente:** 
  - [id_cliente, id_nacional, nombre, forma_de_pago, zona_destino, teléfono, dirección]
  - Requerido para identificar la forma de pago y zona de envío.

- **Pedido:** 
  - [id_pedido, precio_total, id_cliente, id_transportista, productos_pedido, peso_total, destino, fecha_pedido]
  - Requerido para obtener el precio base.

- **Tasa_Efectividad:** 
  - [id_tasa, id_pedido, estado_final, porcentaje_efectividad, fecha_reporte]
  - Requerido para aplicar el multiplicador correspondiente.

- **Tarifa_Envío:** 
  - [id_tarifa, zona_destino, peso_minimo, peso_maximo, tarifa_base]
  - Requerido para calcular el costo de envío.

- **Liquidacion_Cliente:** 
  - [id_liquidacion, id_pedido, id_cliente, precio_pedido, tarifa_envio, total_calculado, forma_pago, estado (Pagada, Pendiente, Vencida), fecha_liquidacion, fecha_pago, transportista_recaudador]
  - Entidad final resultante que registra el monto a cobrar.

- **Nota_Credito:** 
  - [id_nota, id_liquidacion, id_pedido, monto_credito, motivo (Rechazo Parcial, Devolución, Faltante), fecha_emision]
  - Registra ajustes a liquidaciones existentes.

## Success Criteria (mandatory)

- **SC-001:** El 100% de las liquidaciones generadas deben reflejar correctamente el cálculo de (Precio Pedido + Tarifa Envío) * Porcentaje Efectividad sin pérdida de precisión decimal.

- **SC-002:** El sistema debe generar una liquidación en menos de 2 segundos después de recibir la confirmación de tasa de efectividad del Módulo de Logística.

- **SC-003:** Al consultar una liquidación por ID de cliente, el sistema debe devolver el 100% de sus liquidaciones en el primer intento.

- **SC-004:** El 95% de las liquidaciones generadas deben estar correctas en el primer cálculo sin necesidad de notas de ajuste.

- **SC-005:** El sistema debe procesar al menos 1,000 liquidaciones diarias sin fallos críticos.

- **SC-006:** Todas las liquidaciones deben ser rastreables e inmutables para auditoría contable.

- **SC-007:** El tiempo promedio de búsqueda de liquidaciones por rango de fechas NO debe exceder 3 segundos.

- **SC-008:** Las diferencias entre liquidaciones de cliente y transportista deben ser reconciliables al 100% a través de reportes de auditoría.

## Matriz de Liquidación de Cliente

La remuneración a cobrar del cliente se calcula según la efectividad reportada en el Módulo 2:

| Resultado de la Entrega | % Por Cobrar | Acción Financiera |
| :--- | :--- | :--- |
| **Entregado Completo** | 100% | Ingreso total validado. |
| **Rechazo Parcial** | 80% | Generación automática de nota crédito por productos rechazados. |
| **Devolución (Error Empresa)** | 0% | Nota crédito por valor total del pedido + envío. |
| **Faltante de Inventario** | Variable | Nota crédito por valor de productos no entregados. |
| **No Entregado** | Pendiente | Liquidación en estado "En Riesgo" hasta reintento. |