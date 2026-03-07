# Feature Specification: Consultar Pedido para Facturación

**Created**: 07-03-2026  
**Status**: In Development

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Consulta de productos del pedido para facturación (Priority: P1)

Yo como Módulo de Facturación necesito consultar los productos asociados a un pedido específico utilizando su ID. Para obtener los datos de los productos como lista de strings y usarlos como parámetros para generar el PDF de la factura.

**Why this priority**: Es una función crítica para generar facturas válidas; sin los productos no se puede emitir la facturación.

**Independent Test**: Consultar un pedido por ID y verificar que los productos retornados coincidan exactamente con los almacenados en la base de datos del Módulo de Inventario.

**Acceptance Scenarios**:

1. **Scenario**: Consulta exitosa de productos del pedido (lista de strings)
    - **Given**: Existe un pedido con ID válido en la base de datos del Módulo de Gestión de Inventario
    - **When**: El Módulo de Facturación envía una solicitud de consulta con ese ID de pedido
    - **Then**: El sistema retorna una lista de strings donde cada string representa un producto con formato: "id_producto, nombre, cantidad, precio_unitario, subtotal"

2. **Scenario**: Pedido con múltiples productos
    - **Given**: Un pedido contiene múltiples productos diferentes
    - **When**: Se consulta el pedido por su ID
    - **Then**: El sistema retorna todos los productos como lista de strings

---

### User Story 2 - Consulta de valor total del pedido (Priority: P1)

Yo como Módulo de Facturación necesito conocer el valor total del pedido para emitir la factura con el monto correcto. Para garantizar que la factura refleje el valor exacto a cobrar.

**Why this priority**: Es fundamental para la precisión de la facturación.

**Independent Test**: Consultar el valor total de un pedido y verificar que coincida con la suma de los productos.

**Acceptance Scenarios**:

1. **Scenario**: Consulta exitosa del valor total
    - **Given**: Existe un pedido con productos y precios definidos
    - **When**: Se consulta el pedido por su ID
    - **Then**: El sistema retorna el valor total del pedido

---

### User Story 2 - Consulta de ID de cliente del pedido (Priority: P1)

Yo como Sistema Financiero necesito obtener el ID de base de datos del cliente asociado al pedido. Para poder consultar la forma de pago del cliente y asociar la liquidación al cliente correcto.

**Why this priority**: Es necesario para relacionar la liquidación con el cliente y buscar su forma de pago.

**Independent Test**: Consultar un pedido y verificar que el ID de cliente retornado sea el correcto.

**Acceptance Scenarios**:

1. **Scenario**: Consulta exitosa del ID de cliente
    - **Given**: Existe un pedido con ID válido asociado a un cliente
    - **When**: Se consulta el pedido por su ID
    - **Then**: El sistema retorna el ID de base de datos del cliente

---

### User Story 3 - Validación de existencia del pedido (Priority: P1)

Yo como Módulo de Facturación necesito validar que el pedido existe antes de intentar facturar. Para evitar errores y facturas incorrectas.

**Why this priority**: Previene errores en el proceso de facturación.

**Independent Test**: Intentar consultar pedidos con IDs inexistentes y verificar que se retorne el mensaje de error apropiado.

**Acceptance Scenarios**:

1. **Scenario**: Pedido no encontrado
    - **Given**: Se intenta consultar un pedido con un ID que no existe en la base de datos
    - **When**: El Módulo de Facturación envía la solicitud de consulta
    - **Then**: El sistema retorna un error indicando que el pedido no fue encontrado

---

### Edge Cases

- **¿Qué pasa si el ID del pedido es inválido (no es un número)?**
  - El sistema debe validar el formato del ID y retornar un error de validación: "ID de pedido inválido".

- **¿Qué pasa si el pedido existe pero no tiene productos (pedido vacío)?**
  - El sistema debe prohibir la facturacion y lanzar un error para notificarlo

- **¿Qué pasa si la consulta al módulo de inventario falla?**
  - El sistema debe retornar un error de conexión: "Error al consultar el módulo de inventario. Intente más tarde".

- **¿Qué pasa si hay muchos productos en un pedido (más de 100 items)?**
  - El sistema debe retornar todos los productos sin paginación para completar la facturación.

---

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: El sistema DEBE exponer un endpoint (petición GET) que reciba el parámetro `id_pedido` y retorne los productos asociados desde el Módulo de Inventario como una lista de strings.
- **FR-002**: El sistema DEBE retornar la lista de productos donde cada elemento es un string con formato: "id_producto, nombre, cantidad, precio_unitario, subtotal".
- **FR-003**: El sistema DEBE retornar el valor total del pedido calculado.
- **FR-004**: El sistema DEBE validar que el ID del pedido sea un valor numérico válido mayor a cero.
- **FR-005**: El sistema DEBE retornar un mensaje de error claro cuando el pedido no sea encontrado: "Pedido no encontrado con el ID proporcionado".
- **FR-006**: El sistema DEBE incluir la fecha de creación del pedido para referencia en la factura.
- **FR-007**: El sistema DEBE incluir el ID de base de datos del cliente (id_cliente) asociado al pedido para poder consultar su forma de pago.
- **FR-008**: El sistema DEBE rechazar la facturación de pedidos anulados con el error: "El pedido se encuentra anulado y no puede ser facturado".
- **FR-009**: El sistema DEBE rechazar la facturación de pedidos con valor total menor o igual a cero.
- **FR-010**: El sistema DEBE calcular correctamente el subtotal de cada producto (cantidad × precio unitario).
- **FR-011**: El sistema DEBE realizar la consulta de productos en tiempo real al Módulo de Inventario sin almacenar los datos en la base de datos del Módulo de Facturación.
- **FR-012**: El sistema DEBE retornar los datos del pedido (id_pedido, id_cliente, fecha_pedido, estado, precio_total) también como strings para mantener consistencia.

### Key Entities *(include if feature involves data)*

> **Nota**: Estas entidades representan los datos que se consultan del Módulo de Inventario. NO se almacenan en la base de datos del Módulo de Facturación.

- **Pedido** (consultado del Módulo de Inventario): 
  - [id_pedido, id_cliente, fecha_pedido, estado, precio_total]
  - Entidad principal que representa la orden de compra del cliente.

- **Producto** (consultado del Módulo de Inventario): 
  - [id_producto, nombre, precio_unitario]
  - Catálogo de productos disponibles.

- **Productos_Pedido** (consultado del Módulo de Inventario): 
  - [id_pedido, id_producto, cantidad, precio_unitario_en_pedido]
  - Tabla relacional que conecta pedidos con productos.

- **Lista_Productos_Strings** (retorno del endpoint): 
  - Lista de strings donde cada string representa un producto del pedido
  - Formato: "id_producto, nombre_producto, cantidad, precio_unitario, subtotal"
  - Ejemplo: "501,Gaseosa 1L,10,5000,50000"

---

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: El sistema debe retornar los productos y valor total del pedido en menos de 1 segundo después de recibir la solicitud.

- **SC-002**: El 100% de las consultas con IDs válidos deben retornar la información correcta de productos.

- **SC-003**: El valor total retornado debe ser exactamente igual a la suma de los subtotales de todos los productos.

- **SC-004**: El sistema debe retornar un mensaje de error apropiado en menos de 500ms cuando el pedido no existe.

- **SC-005**: La consulta debe funcionar correctamente bajo carga de al menos 100 solicitudes simultáneas.

- **SC-006**: El sistema debe rechazar el 100% de los pedidos anulados o con valor inválido.

---


## Notas de Implementación

- **Consulta en tiempo real**: Los productos del pedido se consultan directamente al Módulo de Inventario cada vez que se necesita generar una factura. NO se almacenan en la base de datos del Módulo de Facturación para evitar duplicidad de información.

- **Lista de strings**: Los productos se retornan como una lista de strings con formato: "id_producto, nombre, cantidad, precio_unitario, subtotal". Esta lista se pasa directamente como parámetro para generar el PDF de la factura.

- **Sin persistencia**: El Módulo de Facturación no guarda los datos del pedido en su propia base de datos. Solo consulta y usa los datos temporalmente para generar la factura.
