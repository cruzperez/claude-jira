# ADR-001: Selección de backend-as-a-service para la base de datos del MVP

* Estado: Aceptada
* Fecha: 2026-07-01

## Contexto

`docs/specs.md` ya fija parte del stack tecnológico (§ "Stack Tecnológico"):

- Backend: Node.js con API REST/GraphQL.
- Base de datos: **PostgreSQL — relacional, elegida para modelar relaciones entre proyectos, tickets y usuarios** (specs.md, línea 30).

Los Objetivos del proyecto (specs.md, línea 8) piden "entregar un primer esqueleto funcional cuanto antes; alcance productivo completo en 3 semanas" para un equipo interno de ~10 personas (specs.md, línea 6). Los Requerimientos Funcionales que más presionan el diseño de datos son:

- RF-01: login de usuarios registrados.
- RF-02, RF-03, RF-04: dos roles con reglas de autorización distintas (admin gestiona todo; usuario normal solo lo que creó).
- RF-05: tickets agrupados por proyecto.
- RF-06: visibilidad de proyectos condicionada a tener tickets asignados.
- RF-08: un ticket asignable a una o más personas.
- RF-10: listado de tickets filtrable.

Dado el plazo ajustado y el tamaño del equipo, se evalúa si conviene apoyarse en un backend-as-a-service (BaaS) para la base de datos y la autenticación en lugar de construir ambas piezas desde cero, y cuál se ajusta mejor a lo ya decidido y requerido en `specs.md`. Esta decisión no reabre lo ya fuera de alcance en specs.md (integraciones complejas de terceros, analytics, multi-tenant).

## Opciones consideradas

### Opción A — Supabase (Postgres administrado + Auth + Row Level Security)

**Pros**

- Está construido sobre PostgreSQL estándar, por lo que **no contradice ni reabre** la decisión ya tomada en specs.md de usar "PostgreSQL — relacional, elegida para modelar relaciones entre proyectos, tickets y usuarios" (línea 30).
- Row Level Security (RLS) de Postgres permite expresar directamente en la capa de datos las reglas de autorización de RF-03 (admin gestiona cualquier ticket o proyecto) y RF-04 (usuario normal solo lo que creó), y la visibilidad de RF-06 (usuario normal solo ve proyectos con tickets asignados), sin duplicar esa lógica en cada endpoint del backend Node.js.
- El modelo relacional resuelve de forma nativa (vía tablas y joins) la relación muchos-a-muchos que exige RF-08 (ticket asignado a una o más personas) y el agrupamiento de RF-05 (tickets por proyecto).
- Genera automáticamente API REST y GraphQL sobre el esquema, en línea con el requerimiento de stack "Backend: Node.js con API REST/GraphQL" (specs.md, línea 29): el backend Node.js puede consumir o envolver esa API en lugar de construirla íntegramente, lo que favorece "entregar un primer esqueleto funcional cuanto antes" (Objetivos, línea 8).
- Incluye autenticación administrada para "usuarios registrados en la aplicación" (RF-01) sin infraestructura propia de sesiones/contraseñas.

**Contras**

- El supuesto `[PENDIENTE]` sobre si el login debe integrarse con cuentas de la empresa (specs.md, línea 34) sigue sin resolver; si se confirma un requisito de SSO corporativo, habrá que validar qué proveedores soporta Supabase Auth antes de cerrar RF-01.
- Es un servicio gestionado por un tercero: introduce una dependencia de disponibilidad externa que specs.md no contempla explícitamente para una herramienta interna de ~10 personas (Objetivos, línea 6). Se registra como riesgo a aceptar, no como bloqueante, dado que no hay ningún RF que exija operación 100% on-premise.

### Opción B — Firebase (Firestore/Realtime Database + Firebase Auth + Cloud Functions)

**Pros**

- Firebase Auth cubre RF-01 (login de usuarios registrados) igual que Supabase.
- Cloud Functions soporta Node.js, compatible en principio con el requerimiento de stack "Backend: Node.js" (specs.md, línea 29).

**Contras**

- Su base de datos principal (Firestore o Realtime Database) es **NoSQL orientada a documentos**, lo que contradice directamente la decisión ya registrada en specs.md de usar "PostgreSQL — relacional" (línea 30). Adoptarla implicaría descartar esa decisión ya tomada o mantener dos fuentes de datos en paralelo.
- Modelar RF-05 (tickets agrupados por proyecto), RF-06 (visibilidad por asignación) y RF-08 (asignación a una o más personas) como relaciones muchos-a-muchos en un modelo de documentos exige desnormalización manual y múltiples lecturas, en lugar de los joins que specs.md ya asume al fijar un modelo relacional.
- RF-10 exige "un listado de tickets filtrable"; las consultas de Firestore no soportan combinar libremente múltiples criterios (specs.md, Supuestos línea 39, menciona fecha, prioridad, responsable, proyecto y etiquetas como candidatos) sin definir de antemano un índice compuesto por cada combinación, lo que complica un filtrado abierto.
- No genera una API REST/GraphQL sobre el esquema como Supabase; esa capa (specs.md, línea 29) habría que construirla íntegramente a mano en el backend Node.js, lo que aporta menos velocidad hacia "un primer esqueleto funcional cuanto antes" (Objetivos, línea 8) que la Opción A.

### Opción C — PostgreSQL autogestionado, sin BaaS (statu quo de specs.md)

**Pros**

- Cumple igual la decisión relacional de specs.md (línea 30), con control total del esquema y sin depender de un proveedor externo.

**Contras**

- Obliga a construir manualmente la autenticación (RF-01), la autorización por rol (RF-02, RF-03, RF-04) y la capa de API REST/GraphQL (specs.md, línea 29) desde cero, lo cual compite directamente con el objetivo de "entregar un primer esqueleto funcional cuanto antes; alcance productivo completo en 3 semanas" (Objetivos, línea 8) para un equipo de ~10 personas.

## Decisión

Se adopta la **Opción A — Supabase** como backend-as-a-service para base de datos y autenticación del MVP.

Justificación basada en requerimientos, no en preferencia:

1. Es la única opción evaluada que no reabre ni contradice la decisión de PostgreSQL relacional ya fijada en specs.md (línea 30).
2. Row Level Security resuelve de forma nativa las reglas de autorización de RF-03, RF-04 y RF-06.
3. El modelo relacional resuelve de forma nativa RF-05 y RF-08 sin desnormalización.
4. La API REST/GraphQL autogenerada acelera el objetivo de entrega temprana (Objetivos, línea 8) manteniendo el stack de backend ya definido (Node.js con API REST/GraphQL, línea 29).

## Consecuencias

**Positivas**

- El equipo Node.js dedica menos esfuerzo a construir autenticación y CRUD base, y más a la lógica específica del dominio (tablero, comentarios, filtros).
- Las reglas de permisos de RF-03/RF-04/RF-06 quedan expresadas cerca de los datos (RLS), reduciendo el riesgo de que frontend y backend implementen la autorización de forma inconsistente.

**Negativas / a vigilar**

- Se acepta una dependencia de disponibilidad y límites de plan de un proveedor externo (Supabase) para una herramienta interna de ~10 personas.
- El pendiente de login corporativo (specs.md, Supuestos línea 34) debe resolverse verificando los proveedores de identidad soportados por Supabase Auth antes de dar RF-01 por cerrado.
- Los demás puntos aún `[PENDIENTE]` en specs.md (campos exactos del ticket, criterios de filtrado, estrategia de concurrencia, cardinalidad de asignación) deberán modelarse dentro del esquema Postgres de Supabase una vez se resuelvan; esta decisión de BaaS no los resuelve por sí sola.
