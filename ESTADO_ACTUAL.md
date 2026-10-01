# Estado del proyecto ComedorApp / AFA — notas entre IAs

Este archivo lo usan dos asistentes distintos (Claude en chat normal, y Claude Cowork) que trabajan
sobre el mismo repositorio y el mismo proyecto Firebase sin verse entre sí. Antes de tocar
`index.html` o las reglas de Firebase, lee esto. Después de tocar algo, añade una línea abajo con
la fecha, quién eres y qué has cambiado.

## Arquitectura vigente (no rediseñar sin comentarlo aquí antes)
- `students/ALU-XXXX`: registro maestro plano de cada alumno (id, nombre, apellidos, clase, alergias,
  tipoPago, becado, becaImporte, activo). El ID es permanente y es la identidad compartida entre
  todos los módulos (comedor, acogida, casal, socios...).
- `roster_by_clase/<clase>/ALU-XXXX: true`: índice de solo lectura que mantiene el propio código
  (sync, importación, alta, baja) para que un profesor pueda leer únicamente su clase sin que las
  reglas de Firebase tengan que anidar por clase. Si añades una vía nueva para crear/mover/borrar
  un alumno, actualiza también este índice.
- `attendance/<fecha>/ALU-XXXX`: asistencia plana por alumno. Profesor la lee/escribe alumno a
  alumno (nunca la fecha entera), usando `roster_by_clase` para saber cuáles son los suyos.
- Menú lateral: registro `AREAS` en el código (busca `const AREAS = {`). Cada módulo nuevo
  (acogida, casal, socios...) se declara ahí con su vista y sus secciones; admin las ve todas
  automáticamente en "Supervisar <Área>", sin tocar más lógica de navegación.
- Reglas de Firebase: el bloque completo vigente debe coincidir con lo publicado en la consola.
  Si lo cambias, pega aquí el bloque completo actualizado, no solo un resumen.

## Historial de cambios
- 2026-09-29, Claude (chat): migración de alumnado/asistencia a estructura plana por ALU-XXXX +
  roster_by_clase. Commit `16b87bba` en index.html.
- 2026-09-29, Cowork: publicadas las reglas para students/ALU-XXXX plano + roster_by_clase.
- 2026-09-29, Claude (chat): cuentas de usuario corto ahora usan afanarcismonturiolroses+<usuario>@gmail.com en vez de @comedorafa.local. Commit `adc744ff`.
- 2026-09-29, Cowork: publicadas las reglas con el rol socios (reglas_v3.json).

- 2026-10-01, Claude (chat): sesión larga con muchos cambios acumulados desde el 29-sep, resumen:
  - **Simplificado**: se eliminó `directorio_alumnos` (el índice "ligero" para Socios sin alergias/becas).
    Ahora Socios, Acogida, Extraescolares y Casal leen `students` directamente, igual que monitora —
    decisión explícita de Carlos tras explicarle que Firebase no permite ocultar campos por rol, solo
    rutas enteras; la ocultación de alergias/becas en esas pantallas es solo de interfaz, no de permiso real.
  - Comedor pasó a ser un área propia (como Tesorería/Socios), accesible por "Supervisar Comedor". El panel
    de admin ("Hoy de un vistazo") quedó solo con el resumen del día + Usuarios y roles.
  - Acogida matinal: sin importar listas. Marta elige del alumnado completo cada mañana (filtro de clase +
    buscador), con "habituales" destacados por frecuencia histórica. Cierre de mes automático: precios
    socio 2€/día·15€ medio mes (12+ días)·20€ mes (20+ días); no socio 3€/día·30€·50€. Pago: Banc Sabadell
    ES90 0081 0213 39 0001554158.
  - Afacasal: precio automático según socio AFA (45€ = 40+5 material) o no socio (80€ = 75+5 material),
    usando el mismo motor genérico de Extraescolares/Acogida (`crearModuloServicio`, ahora con `precioFn`
    opcional). Extraescolares NO lleva pago (lo gestiona cada monitor fuera del AFA); solo difusión/captación.
  - Comunicación: calendario de eventos + tienda (catálogo simple, sin cobro online), visible para
    cualquier rol con sesión iniciada.
  - Incidencias: sistema compartido (`registrarIncidencia`/`pedirIncidencia`) con botón por alumno en
    Comedor (profesor/monitora) y Acogida, y nota genérica en Extraescolares/Casal. Bloque "Incidencias de
    hoy" en el panel de admin con check de resuelta/abierta.
  - **Fallo real corregido**: `renderMonitoraList` leía la asistencia anidada por clase
    (`dayAtt[classId][id]`), un resto de antes de la migración a plano — monitora no veía a nadie marcado
    desde esa migración. Ya lee `dayAtt[id]` directo.
  - Reglas vigentes: **reglas_v10.json** es la versión completa y definitiva con todo lo anterior
    (students ampliado a socios/acogida/extraescolares/casal, incidencias, eventos, tienda, socios
    ampliado a acogida+casal). Publicar esa entera, no las intermedias.

- 2026-10-01, Carlos: aviso para quien retome esto — el proyecto se va a migrar a otra cuenta/sesión de
  Claude que sí tiene conector con Era (para conciliar movimientos bancarios con los pagos previstos de
  socios/acogida/casal). Esa sesión, de momento, NO tiene permiso de escritura sobre este repo de GitHub
  (el token no se lo permite), lo cual limita el trabajo ahí. Si eres esa sesión: coordina con Carlos cómo
  vais a publicar cambios de código mientras tanto (token nuevo, o seguir pidiendo los cambios de index.html
  a esta sesión de chat normal).
