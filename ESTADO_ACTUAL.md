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
