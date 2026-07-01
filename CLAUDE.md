# CLAUDE.md

## Mini Jira

### Objetivo
Construir una herramienta tipo Mini Jira para gestionar tareas, flujos de trabajo y colaboración de forma simple y clara.

### Stack tentativo
- Frontend: React + TypeScript
- Backend: Node.js con API REST/GraphQL
- Base de datos: PostgreSQL, gestionado como Supabase (BaaS) — decisión registrada en `docs/adr/001-database-selection.md`
- Testing: Vitest/Jest

### Convenciones
- Antes de codificar, cada requerimiento o cambio significativo debe producir un artefacto en docs/.
- El archivo specs.md es la fuente única de verdad para alcance, comportamiento y decisiones de producto.
- Mantener cambios pequeños, trazables y bien documentados.

### Estándares de UI
- Cumplir con WCAG 2.1 AA.
- Priorizar usabilidad, claridad y consistencia.
- Usar Design Tokens para colores, tipografía, espaciado y componentes.

### Fuera de alcance
- Integraciones complejas de terceros.
- Funcionalidades de reporting avanzado o analytics empresarial.
- Módulos de pagos, facturación o administración multi-tenant.

### Estado actual
- Proyecto en fase de definición: aún no existen código, `docs/` ni `specs.md`.
- `insumos/` contiene la transcripción del kickoff; úsala como input para redactar `specs.md`, no como fuente de verdad.
- Decisiones que el kickoff dejó abiertas y que `specs.md` debe cerrar antes de codificar: modelo de permisos admin/usuario, qué es un "proyecto" y quién lo ve, número de columnas del tablero, asignación de tickets a una o varias personas, manejo de edición concurrente, y diferencia entre "eliminar" y "archivar".
- Notificaciones por email y dashboard de métricas se discutieron en el kickoff pero quedan fuera de alcance salvo que `specs.md` los incorpore explícitamente.
