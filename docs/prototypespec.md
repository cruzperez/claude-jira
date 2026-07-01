# prototypespec.md — Mini Jira

Especificación de diseño derivada exclusivamente de `docs/specs.md` (requerimientos funcionales) y `architecture/er_diagram.md` (entidades del dominio). Todo el contenido de ejemplo (nombres, textos, personas, tickets) es **placeholder** — no representa datos reales. Este documento alimenta dos consumidores:

- **Claude Code**: usa los Design Tokens (§A) y el inventario de componentes como contrato de implementación (nombres de variables/props, estados a soportar).
- **Google Stitch**: usa el propósito, layout y estados de cada funcionalidad (§C) como prompt estructurado para generar mockups de cada pantalla.

Cada funcionalidad de §C proviene de la sección "In-Scope" y "Requerimientos Funcionales" de `docs/specs.md`; ninguna se inventa fuera de ese documento.

---

## A. Sistema de diseño — Design Tokens

Justificación: RF-14 (estilo visual limpio y moderno, fondo blanco, sombras suaves, usabilidad sobre densidad) y RF-15 (WCAG 2.1 AA + Design Tokens).

### A.1 Color

| Token | Valor | Uso |
|---|---|---|
| `color.bg.default` | `#FFFFFF` | Fondo principal de la aplicación (RF-14: "fondo blanco") |
| `color.bg.subtle` | `#F5F5F7` | Fondo de secciones secundarias (sidebar, paneles) |
| `color.surface.default` | `#FFFFFF` | Fondo de tarjetas (tickets, modales) |
| `color.border.default` | `#E5E5EA` | Bordes de inputs, tarjetas, separadores |
| `color.border.focus` | `#0071E3` | Borde/outline de foco de teclado (WCAG 2.4.7) |
| `color.text.primary` | `#1D1D1F` | Texto principal (contraste ≥ 7:1 sobre `bg.default`) |
| `color.text.secondary` | `#6E6E73` | Texto secundario/metadatos (contraste ≥ 4.5:1 sobre `bg.default`) |
| `color.text.on-accent` | `#FFFFFF` | Texto sobre fondos de color de acento |
| `color.accent.default` | `#0071E3` | Acción primaria (botones, enlaces, elementos activos) |
| `color.accent.hover` | `#0058B0` | Estado hover/active de la acción primaria |
| `color.success.default` | `#1E8E3E` | Confirmaciones (p. ej. ticket guardado) |
| `color.warning.default` | `#FF9500` | Avisos no bloqueantes |
| `color.danger.default` | `#D70015` | Errores, validaciones fallidas, acción "Eliminar" (RF-09) |
| `color.neutral.archived` | `#9E9EA3` | Indicador visual de ticket/proyecto archivado (RF-09) |

> Todos los pares texto/fondo de esta tabla deben validarse con una herramienta de contraste antes de codificar (relación mínima 4.5:1 texto normal, 3:1 texto grande y componentes no textuales — RF-15).

### A.2 Tipografía

| Token | Valor |
|---|---|
| `font.family.base` | `"Inter", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif` |
| `font.size.xs` | `12px` |
| `font.size.sm` | `14px` |
| `font.size.base` | `16px` |
| `font.size.lg` | `18px` |
| `font.size.xl` | `24px` |
| `font.size.2xl` | `32px` |
| `font.weight.regular` | `400` |
| `font.weight.medium` | `500` |
| `font.weight.semibold` | `600` |
| `font.lineHeight.tight` | `1.2` |
| `font.lineHeight.normal` | `1.5` |

`font.size.base` = 16px como mínimo de cuerpo de texto para evitar zoom forzado en móvil y facilitar el redimensionado hasta 200% (WCAG 1.4.4).

### A.3 Espaciado (grid de 4px)

| Token | Valor |
|---|---|
| `space.1` | `4px` |
| `space.2` | `8px` |
| `space.3` | `12px` |
| `space.4` | `16px` |
| `space.5` | `24px` |
| `space.6` | `32px` |
| `space.7` | `48px` |
| `space.8` | `64px` |

### A.4 Radios de borde

| Token | Valor | Uso |
|---|---|---|
| `radius.sm` | `6px` | Inputs, badges |
| `radius.md` | `10px` | Botones, tarjetas de ticket |
| `radius.lg` | `16px` | Modales, paneles grandes |
| `radius.full` | `9999px` | Avatares, píldoras de estado |

### A.5 Sombras (elevación)

Justificación directa: RF-14 exige "sombras suaves".

| Token | Valor | Uso |
|---|---|---|
| `shadow.sm` | `0 1px 2px rgba(0,0,0,0.06)` | Tarjeta de ticket en reposo |
| `shadow.md` | `0 4px 12px rgba(0,0,0,0.08)` | Tarjeta de ticket en hover/drag, dropdowns |
| `shadow.lg` | `0 12px 32px rgba(0,0,0,0.12)` | Modales, diálogos |

### A.6 Tokens semánticos (mapeo para componentes)

| Token semántico | Referencia a token primitivo |
|---|---|
| `button.primary.bg` | `color.accent.default` |
| `button.primary.bg.hover` | `color.accent.hover` |
| `button.danger.bg` | `color.danger.default` |
| `card.bg` | `color.surface.default` |
| `card.shadow` | `shadow.sm` |
| `card.shadow.dragging` | `shadow.md` |
| `focus.ring` | `color.border.focus` |
| `badge.archived.bg` | `color.neutral.archived` |

### A.7 Nota sobre modo oscuro

`docs/specs.md` marca el modo oscuro como deseable pero `[PENDIENTE]` de confirmar para el MVP (línea 18). Los tokens de color se definen como pares semánticos (`bg.default`, `text.primary`, etc.) precisamente para poder añadir un segundo set de valores (tema oscuro) sin cambiar componentes, el día que ese pendiente se resuelva. Este documento **no** define los valores del tema oscuro todavía.

---

## B. Estándares WCAG 2.1 AA + Usabilidad

Justificación: RF-15 (WCAG 2.1 AA) y Objetivos de specs.md (línea 7: "priorizar facilidad de uso... sobre funcionalidad avanzada"; línea 9: estética "moderna, limpia, tipo Apple").

### B.1 Criterios WCAG 2.1 aplicables

| Criterio | Nivel | Aplicación en Mini Jira |
|---|---|---|
| 1.1.1 Contenido no textual | A | Iconos de acción (archivar, mover, comentar) llevan texto alternativo o `aria-label` |
| 1.3.1 Información y relaciones | A | Columnas del tablero, campos de formulario y roles usan HTML semántico/ARIA, no solo posición visual |
| 1.4.1 Uso del color | A | El estado de un ticket (columna, archivado) siempre se indica con texto o icono, nunca solo con color |
| 1.4.3 Contraste mínimo | AA | Todos los pares texto/fondo de §A.1 cumplen 4.5:1 (texto normal) o 3:1 (texto grande) |
| 1.4.4 Redimensionar texto | AA | La interfaz es usable con el texto ampliado al 200% sin pérdida de contenido o funcionalidad |
| 1.4.11 Contraste no textual | AA | Bordes de inputs, iconos de estado y el indicador de foco cumplen 3:1 contra el fondo |
| 2.1.1 Teclado | A | Crear/editar/archivar ticket, mover en el tablero, comentar y filtrar son accesibles sin mouse |
| 2.4.3 Orden de foco | A | El foco sigue el orden lógico de lectura en formularios y modales |
| 2.4.6 Encabezados y etiquetas | AA | Cada pantalla y cada input tienen encabezado/etiqueta descriptivos |
| 2.4.7 Foco visible | AA | Todo elemento interactivo muestra `focus.ring` (§A.6) al recibir foco |
| 3.3.1 Identificación de errores | A | Errores de formulario (p. ej. ticket sin título, RF-07) se describen en texto junto al campo |
| 3.3.2 Etiquetas o instrucciones | A | Todo campo de formulario tiene etiqueta visible, no solo placeholder |
| 4.1.2 Nombre, función, valor | A | Componentes custom (tarjeta arrastrable, dropdown de filtros) exponen rol/estado vía ARIA |

### B.2 Principios de usabilidad

- **Menor densidad, mayor claridad** (RF-14): priorizar espacio en blanco y jerarquía tipográfica sobre mostrar todos los datos posibles en pantalla.
- **Feedback inmediato**: toda acción (guardar ticket, mover en el tablero, comentar) muestra confirmación visual inmediata (ver estados "éxito" en §C).
- **Reversibilidad**: dado que "Eliminar" archiva y no borra (RF-09), la UI debe comunicar explícitamente que la acción es reversible para reducir la fricción de decisión.
- **Consistencia**: un mismo componente (botón primario, tarjeta de ticket, badge de estado) se ve y comporta igual en todas las pantallas.

---

## C. Funcionalidades del MVP

Inventario de componentes base reutilizados en esta sección: `Button` (primary/secondary/danger), `Input`, `Textarea`, `Select`, `Avatar`, `Badge`, `Card`, `Modal`, `Toast`, `TopBar`, `Sidebar`, `EmptyState`.

### C.1 Autenticación (Login) — RF-01

**Propósito**: permitir a un usuario registrado en la aplicación iniciar sesión para acceder a sus proyectos y tickets.

**Componentes**: `Input` (email), `Input` (password), `Button` primary ("Iniciar sesión"), `Toast` (error).

**Layout**:
```
┌───────────────────────────────┐
│         [Logo Mini Jira]      │
│                                │
│   Correo electrónico           │
│   [ usuario.demo@ejemplo.com ] │
│                                │
│   Contraseña                   │
│   [ •••••••••••••••••••••••• ] │
│                                │
│   [   Iniciar sesión   ]       │
└───────────────────────────────┘
```
Formulario centrado, ancho máximo ~360px, fondo `color.bg.default`, sin distracciones adicionales (alineado a RF-14).

**Estados**:
- Vacío/inicial: campos sin contenido, botón habilitado.
- Cargando: botón muestra spinner, campos deshabilitados.
- Error (RF-01, credenciales inválidas): `Toast` de error "Correo o contraseña incorrectos", los campos no se limpian.
- Éxito: redirección al listado de proyectos (§C.2).

---

### C.2 Proyectos — RF-05, RF-06

**Propósito**: agrupar tickets por proyecto y mostrar a cada usuario únicamente los proyectos relevantes para su rol.

**Componentes**: `Sidebar` (lista de proyectos), `Card` (resumen de proyecto), `EmptyState`, `Badge` (contador de tickets).

**Layout**:
```
┌──────────────┬─────────────────────────────┐
│ Proyectos    │  Proyecto Demo A             │
│ ▸ Proyecto A │  ─────────────────────────── │
│   Proyecto B │  [ Tablero ]  [ Tickets ]     │
│   Proyecto C │                              │
│              │  (contenido del tablero o     │
│              │   listado, ver §C.3 / §C.7)   │
└──────────────┴─────────────────────────────┘
```

**Estados**:
- Administrador: la `Sidebar` lista todos los proyectos existentes (RF-06).
- Usuario normal con tickets asignados: la `Sidebar` lista solo los proyectos donde tiene al menos un ticket asignado (RF-06).
- Usuario normal sin ningún ticket asignado: `EmptyState` ("Todavía no tienes proyectos asignados").
- Proyecto archivado (RF-03, admin puede archivar proyectos): aparece con `Badge` "Archivado" (`badge.archived.bg`) y no permite crear tickets nuevos dentro de él.

---

### C.3 Tablero de tickets — RF-11, RF-12

**Propósito**: visualizar y mover tickets de un proyecto entre los estados definidos, para dar seguimiento visual al avance del trabajo.

**Componentes**: 4 columnas fijas (`Card` de columna), `TicketCard` dentro de cada columna, `Badge` de asignados (avatares superpuestos).

**Layout**:
```
┌───────────┬───────────────┬───────────┬───────────┐
│ Por hacer │ En progreso   │ Review    │ Listo     │
├───────────┼───────────────┼───────────┼───────────┤
│ [Ticket]  │ [Ticket]      │ [Ticket]  │ [Ticket]  │
│ [Ticket]  │               │           │ [Ticket]  │
│           │               │           │           │
└───────────┴───────────────┴───────────┴───────────┘
```
Columnas de ancho igual (RF-11: exactamente 4 — "Por hacer", "En progreso", "Review", "Listo"), `TicketCard` con `shadow.sm` en reposo y `shadow.md` al arrastrarse.

**Estados**:
- Columna vacía: `EmptyState` compacto dentro de la columna ("Sin tickets aquí").
- Ticket activo: se puede arrastrar a cualquier otra columna (RF-12).
- Ticket archivado (RF-09): no aparece en el tablero activo (se archiva, no se borra, pero se retira de la vista operativa).
- Sin permiso para mover el ticket (RF-03/RF-04, ver §C.8): la tarjeta no es arrastrable y muestra un cursor deshabilitado.

---

### C.4 Ticket: creación, edición y archivado — RF-07, RF-09

**Propósito**: registrar el trabajo pendiente con los datos mínimos requeridos y permitir su archivado sin pérdida de historial.

**Componentes**: `Modal` (formulario de ticket), `Input` (título), `Textarea` (descripción larga), `Button` primary ("Guardar"), `Button` danger ("Eliminar").

**Layout**:
```
┌──────────────────────────────────────────┐
│  Nuevo ticket                        [x]  │
│  ────────────────────────────────────────│
│  Título                                   │
│  [ Ej: Corregir enlace roto en el footer ]│
│                                            │
│  Descripción                              │
│  [ Lorem ipsum dolor sit amet, consectetur│
│    adipiscing elit...                   ] │
│                                            │
│               [Cancelar]  [ Guardar ]     │
└──────────────────────────────────────────┘
```

**Estados**:
- Creación: campos vacíos, `Button` "Guardar" deshabilitado hasta que título y descripción tengan contenido (RF-07: campos mínimos obligatorios).
- Edición: campos pre-cargados con el contenido actual del ticket.
- Error de validación (RF-07): mensaje inline bajo el campo faltante ("El título es obligatorio").
- Archivado (RF-09): el botón visible dice "Eliminar", pero al confirmar muestra un `Toast` "Ticket archivado" — nunca "Ticket eliminado", para no sugerir borrado físico.
- Solo lectura (sin permiso de edición, ver §C.8): formulario visible pero campos e íconos de acción deshabilitados.

---

### C.5 Asignación de personas — RF-08

**Propósito**: distribuir el trabajo de un ticket entre una o más personas del equipo.

**Componentes**: `Select` multi-selección (buscador de personas), `Avatar` (por persona asignada), `Badge` (contador "+N" si hay más asignados de los que caben en el espacio visible).

**Layout**:
```
Asignado a
[ (Avatar) Jane Doe  x ] [ (Avatar) John Smith  x ] [ + Añadir persona ]
```

**Estados**:
- Sin asignar: se muestra el control "+ Añadir persona" sin avatares.
- Una persona asignada: un `Avatar` con nombre.
- Varias personas asignadas (RF-08): múltiples `Avatar` en fila, con `Badge "+N"` si no caben todos.
- Buscando: el `Select` muestra una lista filtrable de personas mientras se escribe.

---

### C.6 Comentarios — RF-13

**Propósito**: permitir la comunicación de contexto y avances dentro de un ticket.

**Componentes**: `Textarea` (nuevo comentario), `Button` primary ("Comentar"), lista de `Card` de comentario (autor, contenido).

**Layout**:
```
Comentarios
┌────────────────────────────────────────┐
│ (Avatar) Jane Doe                       │
│ Lorem ipsum dolor sit amet.             │
└────────────────────────────────────────┘
┌────────────────────────────────────────┐
│ Escribe un comentario...                │
│                          [ Comentar ]   │
└────────────────────────────────────────┘
```

**Estados**:
- Sin comentarios: `EmptyState` ("Todavía no hay comentarios").
- Comentario vacío: `Button` "Comentar" deshabilitado.
- Enviando: `Button` muestra spinner breve.
- Publicado: el nuevo comentario aparece al final de la lista sin recargar la página.

---

### C.7 Listado de tickets con filtros — RF-10

**Propósito**: encontrar rápidamente tickets dentro de un proyecto aplicando criterios de filtrado.

**Componentes**: barra de filtros (`Select` por criterio), tabla o lista de `TicketCard` compactas, `EmptyState`.

**Layout**:
```
┌───────────────────────────────────────────┐
│ Filtrar por: [ Criterio ▾ ]   [ Limpiar ]  │
├───────────────────────────────────────────┤
│ Ticket #101 — Lorem ipsum...    [Listo]    │
│ Ticket #102 — Lorem ipsum...    [Review]   │
│ Ticket #103 — Lorem ipsum...    [Por hacer]│
└───────────────────────────────────────────┘
```

**Estados**:
- Sin filtro aplicado: se listan todos los tickets visibles para el usuario según su rol (§C.8).
- Filtro aplicado (RF-10): solo se muestran los tickets que cumplen el criterio.
- Sin resultados: `EmptyState` ("Ningún ticket coincide con este filtro").

> `docs/specs.md` marca los criterios exactos de filtrado como `[PENDIENTE]` (línea 39). Este layout usa un único selector genérico "Criterio" como placeholder de interacción; los criterios reales (fecha, prioridad, responsable, proyecto, etiquetas u otros) se añadirán cuando se confirmen.

---

### C.8 Permisos y visibilidad según rol — RF-02, RF-03, RF-04

**Propósito**: que la interfaz refleje visualmente lo que cada rol puede hacer, evitando que un usuario intente una acción que el sistema rechazará.

**Componentes**: no introduce componentes nuevos; define **reglas de estado** transversales sobre los componentes de §C.2 a §C.7.

**Layout**: no aplica (regla transversal, no una pantalla propia).

**Estados**:
- **Administrador** (RF-03): en cualquier ticket o proyecto, los controles de editar/archivar/mover están siempre habilitados, sin importar quién lo creó.
- **Usuario normal, creador del ticket** (RF-04): controles de editar/archivar/mover habilitados.
- **Usuario normal, no creador del ticket** (RF-04): controles de editar/archivar/mover deshabilitados o directamente ocultos (a decidir en implementación), pero el contenido del ticket sigue siendo visible si el ticket está en un proyecto donde el usuario tiene tickets asignados (RF-06).
- **Rol indefinido / sesión no válida**: la aplicación no debe renderizar ninguna de las pantallas de §C.2 a §C.7 (redirige a §C.1).

---

## Notas finales de trazabilidad

- Todas las funcionalidades de §C provienen de la sección "In-Scope" de `docs/specs.md` (líneas 10–18) y sus RF asociados.
- No se incluye una pantalla para "modo oscuro", "notificaciones por email" ni "dashboard de métricas": el primero está `[PENDIENTE]` (línea 18) y los otros dos están explícitamente Out-of-Scope (líneas 21–22).
- No se incluye una pantalla de "creación de proyecto" con reglas de permiso propias, porque `docs/specs.md` marca como `[PENDIENTE]` quién puede crear proyectos (línea 41); el formulario de creación existirá, pero su regla de permisos se define cuando ese pendiente se cierre.
