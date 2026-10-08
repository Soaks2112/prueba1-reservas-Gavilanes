# Especificación de funcionalidad: Reservas de sala

**Rama de funcionalidad**: `001-reservas-sala`
**Creada**: 2026-10-01
**Estado**: Borrador

## Historia de usuario

Como estudiante autenticado, quiero reservar una sala de estudio para un intervalo concreto,
para saber que estará disponible cuando la necesite.

## Reglas de fecha y horario

- Cada reserva corresponde a una sola fecha; no se repite ni puede extenderse a otro día.
- La fecha y las horas se interpretan en la zona horaria local de la sede.
- La fecha no puede estar en el pasado y el inicio no puede ser anterior al momento actual.
- El fin debe ser posterior al inicio.
- No hay una duración mínima o máxima ni un horario comercial adicional. La disponibilidad
  depende de que el intervalo solicitado cumpla las reglas de esta especificación.
- Los intervalos se consideran semiabiertos: incluyen el instante de inicio, pero no el de fin.
  Por ello, una reserva puede empezar exactamente cuando termina otra.

## Escenarios y pruebas de aceptación

### Reservar una sala disponible

**Dado** que el estudiante tiene una sesión autenticada y la Sala A está libre el 14 de octubre
de 2026

**Cuando** solicita reservarla de 09:00 a 10:00 de esa fecha

**Entonces** el sistema registra la reserva a nombre del estudiante autenticado y confirma que se creó.

### Rechazar un intervalo que no es válido

**Dado** que el estudiante tiene una sesión autenticada

**Cuando** solicita una reserva cuyo fin es igual o anterior al inicio

**Entonces** el sistema la rechaza, no la registra y comunica: "La hora de fin debe ser posterior
a la de inicio".

### Rechazar un intervalo que se solapa por el inicio

**Dado** que la Sala A ya está reservada el 14 de octubre de 2026 de 09:00 a 10:00

**Cuando** otro estudiante solicita la Sala A de 09:30 a 10:30 de esa fecha

**Entonces** el sistema rechaza la solicitud, no crea una reserva y comunica exactamente:
"La sala ya está reservada en ese horario."

### Rechazar un intervalo que se solapa por el final

**Dado** que la Sala A ya está reservada el 14 de octubre de 2026 de 09:00 a 10:00

**Cuando** otro estudiante solicita la Sala A de 08:30 a 09:30 de esa fecha

**Entonces** el sistema rechaza la solicitud, no crea una reserva y comunica exactamente:
"La sala ya está reservada en ese horario."

### Rechazar un intervalo que contiene otra reserva

**Dado** que la Sala A ya está reservada el 14 de octubre de 2026 de 09:00 a 10:00

**Cuando** otro estudiante solicita la Sala A de 08:00 a 11:00 de esa fecha

**Entonces** el sistema rechaza la solicitud, no crea una reserva y comunica exactamente:
"La sala ya está reservada en ese horario."

### Permitir reservas consecutivas

**Dado** que la Sala A está reservada el 14 de octubre de 2026 de 09:00 a 10:00

**Cuando** otro estudiante solicita la Sala A de 10:00 a 11:00 de esa fecha

**Entonces** el sistema registra la nueva reserva porque los intervalos no comparten tiempo.

### Permitir el mismo horario en otra sala

**Dado** que la Sala A está reservada el 14 de octubre de 2026 de 09:00 a 10:00

**Cuando** otro estudiante solicita la Sala B de 09:00 a 10:00 de esa fecha

**Entonces** el sistema registra la nueva reserva porque la disponibilidad se determina por sala.

### Rechazar una reserva en el pasado

**Dado** que el estudiante tiene una sesión autenticada

**Cuando** solicita una reserva cuyo inicio es anterior al momento actual en la zona horaria local
de la sede

**Entonces** el sistema la rechaza, no la registra y comunica: "La reserva no puede estar en el
pasado".

### Rechazar una solicitud sin sesión autenticada

**Dado** que el estudiante no tiene una sesión autenticada válida al enviar la solicitud

**Cuando** intenta reservar una sala

**Entonces** el sistema rechaza la solicitud, no registra la reserva y comunica: "Debes iniciar
sesión para reservar".

## Requisitos funcionales

- **FR-001**: El estudiante puede elegir una sala y una fecha con las horas de inicio y fin.
- **FR-002**: El sistema registra la reserva asociada a la identidad autenticada del estudiante;
  el estudiante no puede elegir la identidad a cuyo nombre reserva.
- **FR-003**: El sistema confirma la creación de una reserva aceptada y comunica un motivo claro
  cuando la rechaza.
- **FR-004**: El sistema solo acepta intervalos cuyo fin sea posterior al inicio y cuyo inicio
  no esté en el pasado.
- **FR-005**: El sistema rechaza un intervalo si se solapa con otra reserva de la misma sala y
  fecha. Dos intervalos se solapan cuando el inicio solicitado es anterior al fin existente y
  el inicio existente es anterior al fin solicitado.
- **FR-006**: El rechazo por solapamiento usa exactamente el mensaje "La sala ya está reservada
  en ese horario." y no deja registrada la solicitud rechazada.
- **FR-007**: Las reservas de salas distintas no entran en conflicto, aunque sus horarios
  coincidan.
- **FR-008**: Los intervalos consecutivos de una misma sala se permiten cuando el fin de uno es
  exactamente el inicio del otro.
- **FR-009**: El sistema no acepta reservas sin una sesión autenticada válida.

## Criterios de éxito

- **SC-001**: Todas las solicitudes que cumplen las reglas se registran y reciben confirmación;
  las que incumplen una regla se rechazan y no se registran.
- **SC-002**: En las pruebas de aceptación, el solapamiento se rechaza para intervalos que se
  cruzan parcialmente o que contienen por completo otra reserva; los intervalos consecutivos y
  los de salas distintas se aceptan.
