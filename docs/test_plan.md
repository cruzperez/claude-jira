# test_plan.md — Mini Jira

Plan de pruebas derivado de `docs/backlog.md` (criterios de aceptación en Gherkin) y validado contra `docs/specs.md` (RF). Cada escenario Gherkin tiene al menos un caso de prueba (TC). Casos de prueba adicionales (TC-A) cierran brechas de trazabilidad detectadas durante la revisión. Al final se listan edge cases críticos aún no cubiertos, priorizados por impacto × probabilidad, y la validación de cobertura por RF.

Convención de prioridad de TC: **P0** bloqueante de release, **P1** importante, **P2** deseable.

---

## 1. Casos de prueba por criterio de aceptación

### HU-01 — Inicio de sesión (RF-01)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-001 | Escenario: Inicio de sesión exitoso | Existe un usuario registrado con credenciales válidas | Iniciar sesión con esas credenciales | El usuario accede a la aplicación con los permisos de su rol | P0 |
| TC-002 | Edge case: credenciales inválidas | Existe un usuario registrado | Iniciar sesión con contraseña incorrecta | El sistema rechaza el acceso y no crea sesión | P0 |
| TC-003 | Edge case (nuevo, ver §2) | — | Iniciar sesión con un usuario no registrado | El sistema rechaza el acceso sin revelar si el usuario existe | P1 |

### HU-02 — Permisos de administrador (RF-02, RF-03)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-004 | Escenario: Administrador edita un ticket que no creó | Usuario con rol administrador; ticket creado por otro usuario | Editar título/descripción del ticket | Los cambios se guardan correctamente | P0 |
| TC-005 | Escenario: Administrador archiva un ticket que no creó | Usuario con rol administrador; ticket creado por otro usuario | Ejecutar la acción de archivar sobre el ticket | El ticket queda en estado archivado | P0 |
| TC-006 | Escenario: Administrador gestiona cualquier proyecto | Usuario con rol administrador; existe un proyecto creado por otro usuario/administrador | Crear, editar y archivar ese proyecto | Las tres operaciones se completan sin restricción por autoría | P0 |

### HU-03 — Permisos de usuario normal (RF-02, RF-04)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-007 | Escenario: Usuario normal edita un ticket propio | Usuario con rol normal; es creador del ticket | Editar el ticket | Los cambios se guardan | P0 |
| TC-008 | Escenario: Usuario normal archiva un ticket propio | Usuario con rol normal; es creador del ticket | Archivar el ticket | El ticket queda archivado | P0 |
| TC-009 | Escenario: Usuario normal no puede editar un ticket que no creó | Usuario con rol normal; ticket creado por otro usuario | Intentar editar ese ticket | La operación es rechazada, el ticket no cambia | P0 |
| TC-010 | Escenario: Usuario normal no puede archivar un ticket que no creó | Usuario con rol normal; ticket creado por otro usuario | Intentar archivar ese ticket | La operación es rechazada, el ticket sigue activo | P0 |

### HU-04 — Visibilidad de proyectos según rol (RF-05, RF-06)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-011 | Escenario: Usuario normal ve solo sus proyectos con tickets asignados | Usuario normal con ≥1 ticket asignado en el Proyecto A, sin tickets en el Proyecto B | Consultar la lista de proyectos | Solo aparece el Proyecto A | P0 |
| TC-012 | Escenario: Administrador ve todos los proyectos | Existen varios proyectos de distintos usuarios | Consultar la lista de proyectos como administrador | Aparecen todos los proyectos existentes | P0 |
| TC-013 | Escenario: Los tickets de un proyecto se muestran agrupados | Un proyecto tiene ≥2 tickets asociados | Consultar ese proyecto | Los tickets se listan agrupados bajo ese proyecto y no bajo otro | P1 |
| TC-014 | Edge case: usuario normal sin tickets asignados no ve ningún proyecto | Usuario normal sin ningún ticket asignado | Consultar la lista de proyectos | El listado de proyectos aparece vacío | P0 |

### HU-05 — Creación de tickets (RF-07, RF-05)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-015 | Escenario: Creación de un ticket con los campos mínimos | Usuario dentro de un proyecto | Crear un ticket indicando título y descripción larga | El ticket se registra y queda asociado a ese proyecto | P0 |
| TC-016 | Edge case: crear ticket sin título o sin descripción | Usuario dentro de un proyecto | Intentar crear un ticket omitiendo el título o la descripción | El sistema no registra el ticket | P0 |

### HU-06 — Asignación de tickets a personas (RF-08)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-017 | Escenario: Asignación de un ticket a una persona | Ticket sin asignar | Asignar el ticket a una persona | Esa persona queda registrada como asignada | P0 |
| TC-018 | Escenario: Asignación de un ticket a varias personas | Ticket sin asignar | Asignar el ticket a más de una persona | Todas las personas quedan registradas como asignadas | P0 |
| TC-019 | Edge case: usuario normal intenta asignar un ticket a otra persona | Usuario normal, ticket propio sin asignar | Intentar asignar el ticket a otra persona distinta de sí mismo | **Bloqueado**: comportamiento `[PENDIENTE]` en specs.md — no ejecutable hasta que se defina si un usuario normal puede asignar a terceros | P0 (bloqueante de spec) |

### HU-07 — Archivado de tickets ("Eliminar") (RF-09)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-020 | Escenario: Archivar un ticket mediante la acción "Eliminar" | Usuario con permiso sobre el ticket | Ejecutar "Eliminar" sobre el ticket | El ticket pasa a estado archivado y sigue existiendo en la base de datos (no se borra físicamente) | P0 |
| TC-021 | Edge case: archivar un ticket ya archivado | Ticket ya en estado archivado | Ejecutar "Eliminar" nuevamente sobre ese ticket | El sistema no produce un cambio adicional de estado (operación idempotente) | P1 |

### HU-08 — Listado filtrable de tickets (RF-10)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-022 | Escenario: Filtrar el listado de tickets | Listado con tickets que cumplen y no cumplen un criterio dado | Aplicar el filtro | El listado muestra solo los tickets que cumplen el criterio | P0 |
| TC-023 | Edge case: filtrado sin resultados | Listado de tickets existente | Aplicar un criterio que ningún ticket cumple | El listado se muestra vacío, sin error | P1 |

### HU-09 — Tablero con columnas de estado (RF-11, RF-12)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-024 | Escenario: El tablero muestra las cuatro columnas de estado | Acceso al tablero de un proyecto | Abrir el tablero | Se muestran exactamente las columnas "Por hacer", "En progreso", "Review" y "Listo" | P0 |
| TC-025 | Escenario: Mover un ticket entre columnas del tablero | Ticket activo en una columna | Mover el ticket a otra columna | El estado del ticket cambia al de la columna destino | P0 |
| TC-026 | Edge case: mover un ticket archivado en el tablero | Ticket en estado archivado | Intentar moverlo entre columnas | La operación no se permite | P1 |

### HU-10 — Comentarios en tickets (RF-13)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-027 | Escenario: Añadir un comentario a un ticket | Usuario con acceso al ticket | Añadir un comentario con contenido | El comentario queda registrado en el ticket | P0 |
| TC-028 | Edge case: comentario vacío | Usuario con acceso al ticket | Intentar añadir un comentario sin contenido | El sistema no registra el comentario | P1 |

### HU-11 — Interfaz clara y accesible (RF-14, RF-15)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-029 | Escenario: Navegación completa por teclado | Aplicación cargada | Recorrer todos los elementos interactivos usando solo el teclado (Tab/Shift+Tab/Enter/Espacio) | Todas las acciones críticas (crear, editar, mover, comentar) son alcanzables y ejecutables sin mouse | P1 |
| TC-030 | Escenario: Contraste de color suficiente | Aplicación cargada | Medir el contraste de texto/fondo en cada componente con una herramienta de auditoría WCAG | Todos los componentes cumplen la relación mínima de contraste de WCAG 2.1 AA | P1 |
| TC-A01 *(nuevo — cierra brecha)* | RF-14 no tenía escenario Gherkin propio en backlog.md | Aplicación cargada | Inspeccionar los estilos base de la interfaz (fondo, tipografía, sombras) y verificar el uso de Design Tokens | El fondo es blanco/neutro, las sombras son suaves, y los valores provienen de tokens de diseño (no hardcodeados) | P2 |

### Casos transversales de concurrencia (RF-03, RF-04 — `[PENDIENTE]` en specs.md)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-031 | Edge case: edición concurrente del mismo ticket por dos usuarios | Dos usuarios con permiso sobre el mismo ticket, ambos lo abren para editar | Ambos guardan cambios distintos casi simultáneamente | **Bloqueado**: specs.md marca la estrategia de concurrencia (last-write-wins / bloqueo / merge) como `[PENDIENTE]` — no se puede definir el resultado esperado hasta resolverlo | P0 (bloqueante de spec) |

### Caso de fundación de roles (RF-02)

| ID | Origen | Precondición | Pasos | Resultado esperado | Prioridad |
|---|---|---|---|---|---|
| TC-A02 *(nuevo — cierra brecha)* | RF-02 solo se validaba indirectamente vía TCs de permisos | Existe un mecanismo de gestión de usuarios | Verificar que una cuenta de usuario puede tener asignado el rol "administrador" o el rol "usuario normal" (y ninguno más) | El sistema modela exactamente esos dos roles como valores válidos | P1 |

---

## 2. Edge cases críticos no cubiertos — priorización impacto × probabilidad

Estos casos no están en `docs/backlog.md` ni en la tabla anterior. Se identifican por revisión de riesgo sobre los RF ya definidos, no se inventan features fuera de `specs.md`.

| # | Edge case | RF relacionado | Impacto | Probabilidad | Prioridad | Justificación |
|---|---|---|---|---|---|---|
| E1 | Ticket creado sin ningún asignado queda invisible para todo usuario normal (RF-06 liga visibilidad de proyecto a tener tickets asignados) | RF-06, RF-08 | Alto | Alta | **Crítica** | Es un flujo normal (crear ticket antes de asignarlo). Si nadie lo ve salvo el admin, el equipo pierde trazabilidad de trabajo pendiente sin asignar. Se añade **TC-032** (testable ya, sin pendientes de spec). |
| E2 | Acceso directo a un ticket de un proyecto fuera de la visibilidad del usuario (p. ej. por ID, sin pasar por la UI que ya filtra) | RF-06 | Alto | Media | **Crítica** | Es el vector clásico de IDOR: si la autorización solo se aplica en la UI y no en el backend, un usuario normal podría leer/modificar tickets de proyectos ajenos. Se añade **TC-033**. |
| E3 | Comentario con contenido tipo script/HTML no sanitizado | RF-13 | Alto | Media | **Alta** | Campo de texto libre expuesto a todo el equipo; sin sanitización adecuada es un vector de XSS persistente que afecta a cualquiera que abra el ticket. Se añade **TC-034**. |
| E4 | Archivar un proyecto que tiene tickets activos (RF-03 permite a admin archivar proyectos, pero no se define qué pasa con sus tickets) | RF-03 | Alto | Media | Alta | Riesgo de dejar tickets huérfanos o inaccesibles. **Bloqueado**: specs.md no define el comportamiento; no se puede escribir un TC con resultado esperado hasta resolverlo. |
| E5 | Movimiento concurrente del mismo ticket en el tablero por dos usuarios (misma familia que la concurrencia de edición, pero sobre el estado del tablero) | RF-12 | Medio | Media | Media | Con ~10 personas compartiendo tablero, la colisión es posible pero no constante. Depende de la misma estrategia de concurrencia marcada `[PENDIENTE]` (ver TC-031). |
| E6 | Filtrar combinando varios criterios simultáneamente (p. ej. proyecto + responsable) | RF-10 | Medio | Alta | Alta | Es un uso cotidiano esperable de cualquier listado filtrable, pero los criterios exactos de filtrado están `[PENDIENTE]` en specs.md, por lo que no se puede especificar la combinación válida todavía. |
| E7 | Asignar un ticket a un usuario que fue eliminado/dado de baja de la aplicación | RF-08 | Medio | Baja | Baja | No existe en specs.md un RF de baja de usuarios; el riesgo es real a futuro pero no aplica al MVP tal como está definido. Se deja registrado para revisión post-MVP. |
| E8 | Expiración de sesión mientras el usuario edita un ticket o comentario | RF-01 | Medio | Media | Media | Pérdida de trabajo no guardado; depende del mecanismo de sesión, que a su vez depende del mecanismo de login marcado `[PENDIENTE]` en specs.md. |

**Matriz de referencia (impacto × probabilidad → prioridad):**

| Impacto \ Probabilidad | Baja | Media | Alta |
|---|---|---|---|
| **Alto** | Alta | Crítica | Crítica |
| **Medio** | Baja | Media | Alta |
| **Bajo** | Baja | Baja | Media |

De estos ocho, **E1, E2 y E3 son ejecutables hoy** (no dependen de ningún punto `[PENDIENTE]`) y se incorporan como TC-032 a TC-034 en la tabla de abajo. **E4, E5, E6 y E8 quedan bloqueados** hasta que specs.md resuelva sus respectivos pendientes; **E7** queda registrado como backlog de riesgo post-MVP.

| ID | Edge case | Precondición | Pasos | Resultado esperado |
|---|---|---|---|---|
| TC-032 | E1 — ticket huérfano invisible | Ticket creado en un proyecto sin ningún asignado | Consultar la lista de proyectos como usuario normal sin relación previa con ese proyecto | El proyecto no aparece; el ticket sigue visible para el administrador |
| TC-033 | E2 — acceso directo fuera de alcance | Ticket perteneciente a un proyecto donde el usuario normal no tiene tickets asignados | Solicitar ese ticket directamente (por identificador) sin pasar por el listado filtrado de la UI | El sistema deniega el acceso, independientemente de la ruta usada |
| TC-034 | E3 — comentario con script | Usuario con acceso a un ticket | Añadir un comentario cuyo contenido incluye una etiqueta de script o markup ejecutable | El contenido se almacena y se muestra como texto plano; no se ejecuta ningún script al visualizar el ticket |

---

## 3. Validación final — cobertura de RF

| RF | Descripción breve | TCs asociados | Cobertura |
|---|---|---|---|
| RF-01 | Login de usuarios registrados | TC-001, TC-002, TC-003 | ✅ |
| RF-02 | Dos roles: admin / usuario normal | TC-004 a TC-010 (indirecto vía permisos), TC-A02 (directo) | ✅ |
| RF-03 | Admin gestiona cualquier ticket/proyecto | TC-004, TC-005, TC-006 | ✅ |
| RF-04 | Usuario normal solo gestiona lo propio | TC-007 a TC-010 | ✅ |
| RF-05 | Tickets agrupados por proyecto | TC-013, TC-015 | ✅ |
| RF-06 | Visibilidad de proyectos según rol | TC-011, TC-012, TC-014, TC-032, TC-033 | ✅ |
| RF-07 | Ticket con título y descripción mínimos | TC-015, TC-016 | ✅ |
| RF-08 | Asignación a una o más personas | TC-017, TC-018, TC-019 (bloqueado) | ✅ |
| RF-09 | "Eliminar" archiva, no borra | TC-020, TC-021, TC-026 | ✅ |
| RF-10 | Listado de tickets filtrable | TC-022, TC-023 | ✅ |
| RF-11 | Tablero con 4 columnas | TC-024 | ✅ |
| RF-12 | Mover ticket entre columnas | TC-025, TC-026 | ✅ |
| RF-13 | Comentarios en tickets | TC-027, TC-028, TC-034 | ✅ |
| RF-14 | Estilo visual limpio y moderno | TC-A01 | ⚠️ Cubierto solo tras cerrar brecha (ver nota) |
| RF-15 | WCAG 2.1 AA + Design Tokens | TC-029, TC-030 | ✅ |

**Resultado de la validación:** con los casos de esta tabla, **todos los RF (RF-01 a RF-15) tienen al menos un caso de prueba asociado**.

**Nota de auditoría:** al construir este plan se detectó que `docs/backlog.md` no incluía ningún escenario Gherkin para **RF-14** (HU-11 lo cita como RF de origen, pero sus dos escenarios cubren únicamente RF-15). Se cerró la brecha añadiendo **TC-A01** directamente desde el texto del RF. Se recomienda actualizar `docs/backlog.md` para incluir un escenario Gherkin explícito de RF-14 y mantener la trazabilidad completa en la fuente, no solo en el plan de pruebas.

**Pendientes que bloquean la ejecución (no la escritura) de casos de prueba:** TC-019 y TC-031, y los edge cases E4, E5, E6 y E8, dependen de puntos marcados `[PENDIENTE]` en `docs/specs.md` (asignación por usuario normal, estrategia de concurrencia, campos/criterios de filtrado, mecanismo de sesión). Se recomienda resolverlos antes de la fase de diseño técnico, tal como ya indica specs.md en su sección "Pendientes a resolver antes de detallar diseño técnico".
