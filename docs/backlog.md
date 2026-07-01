# backlog.md — Mini Jira

Historias de usuario críticas del MVP, derivadas exclusivamente de `docs/specs.md`. Cada historia referencia el Requerimiento Funcional (RF) que la origina. Formato declarativo: los escenarios describen QUÉ ocurre, no cómo se implementa en la UI.

---

## HU-01 — Inicio de sesión

Como usuario registrado quiero iniciar sesión en la aplicación para acceder a mis tickets y proyectos.

**RF de origen:** RF-01

```gherkin
Escenario: Inicio de sesión exitoso
  Dado que soy un usuario registrado en la aplicación
  Cuando inicio sesión con credenciales válidas
  Entonces accedo a la aplicación con los permisos correspondientes a mi rol
```

---

## HU-02 — Permisos de administrador sobre tickets y proyectos

Como administrador quiero crear, editar y archivar cualquier ticket o proyecto para gestionar el trabajo del equipo sin restricciones de autoría.

**RF de origen:** RF-02, RF-03

```gherkin
Escenario: Administrador edita un ticket que no creó
  Dado que tengo el rol de administrador
  Y existe un ticket creado por otro usuario
  Cuando edito ese ticket
  Entonces el sistema guarda los cambios

Escenario: Administrador archiva un ticket que no creó
  Dado que tengo el rol de administrador
  Y existe un ticket creado por otro usuario
  Cuando archivo ese ticket
  Entonces el ticket queda archivado

Escenario: Administrador gestiona cualquier proyecto
  Dado que tengo el rol de administrador
  Cuando creo, edito o archivo un proyecto
  Entonces la operación se realiza sin importar quién administra ese proyecto
```

---

## HU-03 — Permisos de usuario normal sobre sus propios tickets

Como usuario normal quiero crear, editar y archivar únicamente los tickets que yo mismo creé para mantener control sobre mi propio trabajo sin afectar el de otros.

**RF de origen:** RF-02, RF-04

```gherkin
Escenario: Usuario normal edita un ticket propio
  Dado que tengo el rol de usuario normal
  Y soy el creador de un ticket
  Cuando edito ese ticket
  Entonces el sistema guarda los cambios

Escenario: Usuario normal archiva un ticket propio
  Dado que tengo el rol de usuario normal
  Y soy el creador de un ticket
  Cuando archivo ese ticket
  Entonces el ticket queda archivado

Escenario: Usuario normal no puede editar un ticket que no creó
  Dado que tengo el rol de usuario normal
  Y existe un ticket creado por otro usuario
  Cuando intento editar ese ticket
  Entonces el sistema rechaza la operación

Escenario: Usuario normal no puede archivar un ticket que no creó
  Dado que tengo el rol de usuario normal
  Y existe un ticket creado por otro usuario
  Cuando intento archivar ese ticket
  Entonces el sistema rechaza la operación
```

---

## HU-04 — Visibilidad de proyectos según rol

Como usuario quiero ver solo los proyectos relevantes para mi rol para no perder tiempo navegando información que no me corresponde.

**RF de origen:** RF-05, RF-06

```gherkin
Escenario: Usuario normal ve solo sus proyectos con tickets asignados
  Dado que tengo el rol de usuario normal
  Y tengo al menos un ticket asignado en un proyecto
  Cuando consulto la lista de proyectos
  Entonces veo únicamente los proyectos donde tengo al menos un ticket asignado

Escenario: Administrador ve todos los proyectos
  Dado que tengo el rol de administrador
  Cuando consulto la lista de proyectos
  Entonces veo todos los proyectos existentes

Escenario: Los tickets de un proyecto se muestran agrupados
  Dado que un proyecto tiene tickets asociados
  Cuando consulto ese proyecto
  Entonces sus tickets se muestran agrupados bajo ese proyecto
```

---

## HU-05 — Creación de tickets

Como usuario quiero crear un ticket con título y descripción para registrar trabajo pendiente en un proyecto.

**RF de origen:** RF-07, RF-05

```gherkin
Escenario: Creación de un ticket con los campos mínimos
  Dado que estoy dentro de un proyecto
  Cuando creo un ticket indicando título y descripción larga
  Entonces el ticket queda registrado y asociado a ese proyecto
```

---

## HU-06 — Asignación de tickets a personas

Como usuario quiero asignar un ticket a una o más personas para distribuir el trabajo del equipo.

**RF de origen:** RF-08

```gherkin
Escenario: Asignación de un ticket a una persona
  Dado que tengo un ticket sin asignar
  Cuando asigno ese ticket a una persona
  Entonces esa persona queda registrada como asignada al ticket

Escenario: Asignación de un ticket a varias personas
  Dado que tengo un ticket sin asignar
  Cuando asigno ese ticket a más de una persona
  Entonces todas esas personas quedan registradas como asignadas al ticket
```

> Nota de trazabilidad: la cardinalidad exacta de asignación y si un usuario normal puede asignar tickets a otros (o solo a sí mismo) está marcada `[PENDIENTE]` en specs.md (Supuestos). Los escenarios anteriores cubren únicamente lo confirmado por RF-08.

---

## HU-07 — Archivado de tickets ("Eliminar")

Como usuario con permiso sobre un ticket quiero archivarlo en lugar de borrarlo para conservar el historial de trabajo.

**RF de origen:** RF-09

```gherkin
Escenario: Archivar un ticket mediante la acción "Eliminar"
  Dado que tengo permiso para eliminar un ticket
  Cuando ejecuto la acción "Eliminar" sobre ese ticket
  Entonces el ticket pasa a estado archivado
  Y el ticket no se borra físicamente del sistema
```

---

## HU-08 — Listado filtrable de tickets

Como usuario quiero filtrar el listado de tickets para encontrar rápidamente los que me interesan.

**RF de origen:** RF-10

```gherkin
Escenario: Filtrar el listado de tickets
  Dado que existe un listado de tickets
  Cuando aplico un criterio de filtrado sobre el listado
  Entonces el listado muestra únicamente los tickets que cumplen ese criterio
```

> Nota de trazabilidad: los criterios exactos de filtrado (fecha, prioridad, responsable, proyecto, etiquetas) están marcados `[PENDIENTE]` en specs.md (Supuestos). El escenario anterior valida el comportamiento genérico exigido por RF-10, no criterios específicos.

---

## HU-09 — Tablero con columnas de estado

Como usuario quiero ver los tickets organizados en un tablero por estado para conocer el avance del trabajo.

**RF de origen:** RF-11, RF-12

```gherkin
Escenario: El tablero muestra las cuatro columnas de estado
  Dado que accedo al tablero
  Entonces veo las columnas "Por hacer", "En progreso", "Review" y "Listo"

Escenario: Mover un ticket entre columnas del tablero
  Dado que un ticket se encuentra en una columna del tablero
  Cuando muevo ese ticket a otra columna
  Entonces el estado del ticket cambia al de la columna de destino
```

---

## HU-10 — Comentarios en tickets

Como usuario quiero añadir comentarios dentro de un ticket para comunicar contexto o avances al equipo.

**RF de origen:** RF-13

```gherkin
Escenario: Añadir un comentario a un ticket
  Dado que tengo acceso a un ticket
  Cuando añado un comentario a ese ticket
  Entonces el comentario queda registrado dentro del ticket
```

---

## HU-11 — Interfaz clara y accesible

Como usuario quiero que la interfaz sea clara, moderna y accesible para poder usar la aplicación sin fricción ni barreras.

**RF de origen:** RF-14, RF-15

```gherkin
Escenario: Navegación completa por teclado
  Dado que uso la aplicación exclusivamente con teclado
  Cuando navego por los elementos interactivos de la interfaz
  Entonces puedo acceder a toda la funcionalidad sin necesidad de un mouse

Escenario: Contraste de color suficiente
  Dado que reviso los elementos visuales de la interfaz
  Cuando se evalúa el contraste entre texto y fondo
  Entonces cumple la relación de contraste mínima exigida por WCAG 2.1 AA
```

---

## Edge Cases y Escenarios de Fallo (deducidos)

Escenarios no enunciados explícitamente en specs.md pero que se derivan lógicamente de los RF y de los puntos marcados `[PENDIENTE]`. Se incluyen para que queden visibles antes de codificar, sin asumir una resolución de los pendientes.

```gherkin
Escenario: Intento de inicio de sesión con credenciales inválidas
  # Contraparte de fallo de RF-01
  Dado que soy un usuario de la aplicación
  Cuando intento iniciar sesión con credenciales inválidas
  Entonces el sistema rechaza el acceso

Escenario: Usuario normal sin tickets asignados no ve ningún proyecto
  # Consecuencia de RF-06
  Dado que tengo el rol de usuario normal
  Y no tengo ningún ticket asignado en ningún proyecto
  Cuando consulto la lista de proyectos
  Entonces no veo ningún proyecto en el listado

Escenario: Intento de crear un ticket sin título o sin descripción
  # Consecuencia de RF-07 (campos mínimos obligatorios)
  Dado que estoy creando un ticket
  Cuando omito el título o la descripción larga
  Entonces el sistema no permite registrar el ticket

Escenario: Filtrado sin resultados
  # Consecuencia de RF-10
  Dado que existe un listado de tickets
  Cuando aplico un criterio de filtrado que ningún ticket cumple
  Entonces el listado se muestra vacío

Escenario: Edición concurrente del mismo ticket por dos usuarios
  # specs.md marca la estrategia de concurrencia como [PENDIENTE]
  Dado que dos usuarios abren el mismo ticket para editarlo al mismo tiempo
  Cuando ambos guardan cambios
  Entonces el comportamiento del sistema queda [PENDIENTE] de definir en specs.md

Escenario: Usuario normal intenta asignar un ticket a otra persona
  # specs.md marca como [PENDIENTE] si un usuario normal puede asignar a otros o solo a sí mismo
  Dado que tengo el rol de usuario normal
  Cuando intento asignar un ticket a otra persona distinta de mí
  Entonces el comportamiento permitido queda [PENDIENTE] de definir en specs.md

Escenario: Intento de mover un ticket archivado en el tablero
  # Consecuencia de RF-09 + RF-12: un ticket archivado no debería seguir activo en el tablero
  Dado que un ticket está archivado
  Cuando intento moverlo entre columnas del tablero
  Entonces el sistema no permite la operación

Escenario: Intento de archivar un ticket ya archivado
  # Consecuencia de RF-09
  Dado que un ticket ya se encuentra archivado
  Cuando ejecuto nuevamente la acción "Eliminar" sobre ese ticket
  Entonces el sistema no produce un cambio adicional de estado

Escenario: Comentario vacío
  # Consecuencia de RF-13
  Dado que tengo acceso a un ticket
  Cuando intento añadir un comentario sin contenido
  Entonces el sistema no registra el comentario
```
