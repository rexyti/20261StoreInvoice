# Feature Specification: [Consultar liquidaciones]

**Created**: 21-02-2026  

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Consultar liquidaciones (Priority: P1)

Yo como contador necesito revisar todas las liquidaciones en la base de datos. Para fines legales.

**Why this priority**: Transparencia de información.

**Independent Test**: Que reciba todas las liquidaciones de la base de datos.

**Acceptance Scenarios**:

1. **Scenario**: Consulta exitosa
   - **Given**: Sesion iniciada en la pagina; Debe haber liquidaciones. 
   - **When**: Cuando las necesite para contabilizacion
   - **Then**: Se muestra los datos de las liquidaciones

---

### User Story 2 - Consulta de liquidaciones de cliente especifico (Priority: P2)

Yo como contador quiero consultar liquidaciones de un Cliente en especifico (se usa ID nacional). Para consultar información especifica con
fines legales

**Why this priority**: Transparencia de la información.

**Independent Test**: Buscar las liquidaciones de un usuario especifico y que concuerden con la base de datos. 

**Acceptance Scenarios**:

1. **Scenario**: Cliente no existente 
   - **Given**: Intento de busqueda de las liquidaciones de un cliente. 
   - **When**: El contador ingresa un ID nacional que no existe en la base de datos.
   - **Then**: El sistema muestra mensaje de cliente no registrado.

---

### User story 3 - Consultar liquidaciones en rango de fechas (Priority: P)

Como contador quiero consultar liquidaciones en un rango determinado de fechas con el fin buscar información según sea requerida en un periodo contable.

**Why this priority**: Transparencia de la información.

**Independient Test**: Buscar liquidaciones en un rango de fechas determinados y que los resultados concuerden con lo requerido.

 **Acceptance scenarios** :
 1. **Scenario**: Consulta exitosa
   - **Given**: Existen liquidaciones en un rango determinado de fecha.
   - **When**: El usuario busca liquidaciones en dicho rango.
   - **Then**: El sistema muestra las liquidaciones correspondientes.

### Edge Cases

- What happens when [El usuario ingresa un ID nacional de un cliente que no existe]?
- How does system handle [El sistema muestra mensaje de error y recomienda registrar al nuevo cliente en una ventana modal]?

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST [Permitir buscar las liquidaciones de un usuario en especifico con su ID nacional (id, fecha, total)]
- **FR-002**: System MUST [Mostrar todas las liquidaciones ordenadas en orden cronologíco descendente]  
- **FR-003**: System MUST [Listar todas las liquidaciones en un rango de fechas ingresado por el contador]
- **FR-004**: System MUST [Permitir buscar las liquidaciones de un usuario en especifico con su ID nacional en un rango de fechas]
- **FR-005**: System MUST [Permitir descargar las liquidaciones]
- **FR-006**: System MUST [El sistema debe mostrar todas las liquidaciones en orden cronológico descendente, 20 por página]


### Key Entities *(include if feature involves data)*

- **[liquidación]**: id_liquidación, id_cliente, total, fecha, id_pedido 1-> 1 con pedido
- **[pedido]**: [id_pedido , precio, productos_pedido , peso, destino, id_cliente]
- **[producto]**: [id_producto, nombre, peso, precio_unitario, descripción]
- **[productos_del_pedido]**: id_producto  n -> 1 con pedido, cantidad, id_pedido n -> 1 con pedido.


## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Al consultar liquidaciones entre dos fechas el sistema debe mostrar el 100% de las liquidaciones existentes desde las 00:00 de la fecha inicial hasta las 23:59 de la fecha final.
- **SC-002**: El sistema debe demorar maximo 2 segundos en mostrar los resultados de busqueda.

