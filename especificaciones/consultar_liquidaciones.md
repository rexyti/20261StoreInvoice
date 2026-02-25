# Feature Specification: [Consultar liquidaciones]

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

Yo como contador necesito revisar todas las liquidaciones en la base de datos. Para fines legales.

**Why this priority**: Transparencia de información.

**Independent Test**: Que reciba todas las liquidaciones de la base de datos.

**Acceptance Scenarios**:

1. **Scenario**: Consulta exitosa
   - **Given**: Sesion iniciada en la pagina; Debe haber liquidaciones. 
   - **When**: Cuando las necesite para contabilizacion
   - **Then**: Se muestra los datos de las liquidaciones

   
---

### User Story 2 - Consulta de liquidaciones (Priority: P2)

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

- What happens when []?
- How does system handle []?

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST [Permitir buscar las liquidaciones de un usuario en especifico con su ID nacional (id, fecha, total)]
- **FR-002**: System MUST [Mostrar todas las liquidaciones ordenadas en orden cronologíco descendente]  
- **FR-003**: System MUST [Listar todas las liquidaciones en un rango de fechas ingresado por el contador]
- **FR-004**: System MUST [Permitir buscar las liquidaciones de un usuario en especifico con su ID nacional en un rango de fechas]
- **FR-005**: System MUST [Permitir descargar las liquidaciones]

### Key Entities *(include if feature involves data)*

- **[liquidación]**: id_liquidación, id_cliente, total, fecha, id_pedido 1-> 1 con pedido
- **[pedido]**: [id_pedido , precio, productos_pedido , peso, destino, id_cliente]
- **[producto]**: [id_producto, nombre, peso, precio_unitario, descripción]
- **[productos_del_pedido]**: id_producto  n -> 1 con pedido, cantidad, id_pedido n -> 1 con pedido.


## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: [Measurable metric, e.g., "Users can complete account creation in under 2 minutes"]
- **SC-002**: [Measurable metric, e.g., "System handles 1000 concurrent users without degradation"]
- **SC-003**: [User satisfaction metric, e.g., "90% of users successfully complete primary task on first attempt"]
- **SC-004**: [Business metric, e.g., "Reduce support tickets related to [X] by 50%"]

