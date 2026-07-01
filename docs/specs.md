# specs.md — Mini Jira

Fuente única de verdad para alcance, comportamiento y decisiones de producto. Toda funcionalidad debe poder trazarse a: la transcripción del kickoff (`insumos/Transcripción Reunión MiniJira.txt`) o a las respuestas del PO durante la Fase 1 de definición (2026-07-01).

## Objetivos
- Construir una herramienta interna de gestión de tickets (tipo Jira simplificado) para un equipo de ~10 personas (transcripción, línea 6).
- Priorizar facilidad de uso y una estética moderna, limpia, tipo Apple, sobre funcionalidad avanzada (transcripción, líneas 9, 55).
- Entregar un primer esqueleto funcional cuanto antes; alcance productivo completo en 3 semanas (transcripción, líneas 6, 53, 58).

## In-Scope
- Gestión de tickets: crear, editar, archivar (transcripción, línea 18; ver RF-09).
- Tickets agrupados por proyecto (transcripción, línea 19).
- Tablero con estados por columnas (transcripción, líneas 28–31; respuesta PO Fase 1).
- Asignación de personas a tickets (transcripción, línea 18).
- Comentarios dentro de un ticket (transcripción, línea 40).
- Roles de usuario: administrador y usuario normal (transcripción, línea 15; respuesta PO Fase 1).
- Filtros sobre el listado de tickets (transcripción, líneas 26–27).
- Modo oscuro (transcripción, línea 64) — mencionado como deseable, no confirmado como obligatorio → `[PENDIENTE]`.

## Out-of-Scope
- Notificaciones por email (respuesta PO Fase 1: "Fuera de alcance (Fase 2)"; objeción técnica en transcripción, línea 41).
- Dashboard de métricas y analytics de tickets cerrados por mes/proyecto (respuesta PO Fase 1: "Fuera de alcance (Fase 2)"; transcripción, línea 44).
- Integraciones complejas de terceros (CLAUDE.md).
- Reporting avanzado o analytics empresarial (CLAUDE.md).
- Módulos de pagos, facturación o administración multi-tenant (CLAUDE.md).

## Stack Tecnológico
- Frontend: React + TypeScript (CLAUDE.md).
- Backend: Node.js con API REST/GraphQL (CLAUDE.md).
- Base de datos: PostgreSQL — relacional, elegida para modelar relaciones entre proyectos, tickets y usuarios (transcripción, línea 34).
- Testing: Vitest/Jest (CLAUDE.md).

## Supuestos
- El login usa cuentas propias de la aplicación; no se confirmó si se integra con cuentas de la empresa (transcripción, línea 14) → `[PENDIENTE]`.
- "Eliminar" en la UI no borra físicamente: archiva el ticket. El botón se etiqueta "Eliminar" por claridad de usuario, pero la acción real es archivar (transcripción, líneas 49–50).
- El manejo de ediciones concurrentes sobre un mismo ticket (last-write-wins vs. bloqueo vs. merge) no se definió; el equipo lo trató como caso de borde a resolver después (transcripción, líneas 38–39) → `[PENDIENTE]`.
- Un ticket puede asignarse a una o varias personas; y si un usuario normal puede asignar tickets a otros o solo a sí mismo, no se definió (transcripción, línea 24) → `[PENDIENTE]`.
- Los campos exactos del ticket más allá de título y descripción larga (p. ej. prioridad, etiquetas, fecha límite) no se definieron (transcripción, línea 26) → `[PENDIENTE]`.
- Los criterios exactos de filtrado más allá de los mencionados (fecha, prioridad, responsable, proyecto, etiquetas) no se confirmaron uno a uno (transcripción, línea 27) → `[PENDIENTE]`.
- Un ticket pertenece a un único proyecto (no se definió si puede pertenecer a varios) → `[PENDIENTE]` (transcripción, línea 20).
- ¿Quién puede crear proyectos? No se definió (transcripción, línea 20) → `[PENDIENTE]`.

## Requerimientos Funcionales

### Autenticación y roles
- **RF-01**: El sistema debe permitir el login de usuarios registrados en la aplicación. (transcripción, línea 14; supuesto de login propio marcado `[PENDIENTE]` arriba)
- **RF-02**: Deben existir dos roles: administrador y usuario normal. (transcripción, línea 15; respuesta PO Fase 1: "Admin gestiona todo, usuario solo lo propio")
- **RF-03**: Un administrador puede crear, editar y archivar cualquier ticket o proyecto, sin importar quién lo creó. (respuesta PO Fase 1)
- **RF-04**: Un usuario normal solo puede crear, editar y archivar los tickets que él mismo creó. (respuesta PO Fase 1; transcripción, línea 49: "Solo quien lo creó. O el admin.")

### Proyectos
- **RF-05**: Los tickets deben agruparse por proyecto. (transcripción, línea 19)
- **RF-06**: Un usuario normal solo debe ver los proyectos donde tiene al menos un ticket asignado; un administrador ve todos los proyectos. (respuesta PO Fase 1: "Solo ve los proyectos donde tiene tickets asignados")

### Tickets
- **RF-07**: Un ticket debe tener al menos los campos: título y descripción larga. (transcripción, línea 26)
- **RF-08**: Un ticket debe poder asignarse a una o más personas. (transcripción, línea 18; cardinalidad exacta `[PENDIENTE]`, ver Supuestos)
- **RF-09**: El botón "Eliminar" de un ticket no debe borrarlo físicamente, sino archivarlo. (transcripción, líneas 49–50)
- **RF-10**: Debe existir un listado de tickets filtrable. (transcripción, línea 26)

### Tablero (board)
- **RF-11**: El tablero debe tener 4 columnas de estado: Por hacer, En progreso, Review, Listo. (respuesta PO Fase 1: "4 columnas (+ Review antes de Listo)"; transcripción, línea 29)
- **RF-12**: Un ticket debe poder moverse entre las columnas del tablero. (transcripción, líneas 28–31, implícito en "tablero" y "estados")

### Comentarios
- **RF-13**: Un usuario debe poder añadir comentarios dentro de un ticket. (transcripción, línea 40)

### UI
- **RF-14**: La interfaz debe seguir un estilo visual limpio y moderno (fondo blanco, sombras suaves), priorizando usabilidad sobre densidad de información. (transcripción, línea 55; CLAUDE.md — Estándares de UI)
- **RF-15**: La interfaz debe cumplir WCAG 2.1 AA y usar Design Tokens. (CLAUDE.md — Estándares de UI)

## Pendientes a resolver antes de detallar diseño técnico
1. Mecanismo de login (cuentas propias vs. cuentas de empresa).
2. Cardinalidad de asignación de tickets y permiso de un usuario normal para asignar a otros.
3. Estrategia de concurrencia en edición simultánea de un ticket.
4. Campos completos del ticket (prioridad, etiquetas, fecha límite, etc.) y criterios de filtrado exactos.
5. Si un ticket pertenece a un único proyecto o puede estar en varios.
6. Quién puede crear proyectos.
7. Si el modo oscuro es obligatorio para el MVP o "nice to have".
