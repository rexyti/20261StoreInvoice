# Feature Specification: [Consultar liquidacion persona]

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

Yo como cliente deseo consultar mis liquidaciones personales en una lista paginada. Para tener informacion de mis pagos

**Why this priority**: Transparencia de informacion

**Independent Test**: Consultar una liquidacion y recibir la informacion en pantalla

**Acceptance Scenarios**:

1. **Scenario**: Consulta exitosa
   - **Given**: Usuario registado; Tener liquidaciones generadas; 
   - **When**: Cuando acceda a "Mis liquidaciones"
   - **Then**: Se muestra los datos de la liquidacion en una lista paginada, donde cada pagina puede almacenar 20 registros ordenada de manera cronologica descendente

   
---



### Edge Cases

- What happens when no haya liquidaciones asociadas a un usuario para listar?
- How does system handle?: Mostrar un mensaje para notificar que no tiene liquidaciones asociadas

## Requirements *(mandatory)*

<!--
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right functional requirements.
-->

### Functional Requirements

- **FR-001**: System MUST implementar la visualizacion de las liquidaciones asociadas a un usuario ordenada cronologicamente descendente. Registrando 20 registros por pagina
- **FR-002**: System MUST Descargar las liquidaciones seleccionadas en formato pdf
- **FR-003**: Users MUST be able to Permitir al usuario navegar entre las paginas de la lista paginada

### Key Entities *(include if feature involves data)*

- **[Liquidacion]**: ID_Liquidacion, Precio, Accion financiera, Id_Cliente, Origen_ciudad, Destinacion_ciudad;
- **[Productos del pedido]**: ID_producto,  Precio_unitario, Cantidad_productos, M -> 1 [Liquidacion] as ID_producto

## Success Criteria *(mandatory)*

<!--
  ACTION REQUIRED: Define measurable success criteria.
  These must be technology-agnostic and measurable.
-->

### Measurable Outcomes

- **SC-001**: El sistema debe listar el 100% de las liquidaciones asociadas a un usuario 
- **SC-002**: El tiempo de respuesta al consultar "Mis liquidaciones" debe ser menor a 3 segundos
- **SC-003**: El 90% de los usuarios debe poder descargar su ultima liquidacion en menos de 3 clics
- **SC-004**: El pdf descargado debe contener los mismos datos que los mostrados en la interfaz web

