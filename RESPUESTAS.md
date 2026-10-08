## Escenarios que elegí y por qué

Probé varios horarios que se cruzan para asegurar que se rechacen, incluso cuando uno queda dentro de otro (`specs/001-reservas-sala/spec.md:43`, `specs/001-reservas-sala/spec.md:52`, `specs/001-reservas-sala/spec.md:61`). También comprobé que sí se pueda reservar justo después de otra reserva o el mismo horario en otra sala (`specs/001-reservas-sala/spec.md:70`, `specs/001-reservas-sala/spec.md:78`).

## Riesgo más grave del repositorio

La pantalla permite cambiar el ID del usuario y usarlo al crear una reserva (`lib/presentation/reserva_page.dart:88`, `lib/presentation/reserva_page.dart:60`, `lib/presentation/reserva_page.dart:62`). Además, Supabase no comprueba que ese ID sea del usuario que inició sesión (`supabase/migracion.sql:21`, `supabase/migracion.sql:24`). Esto permite crear reservas a nombre de otra persona.

## ¿La regla protege la app real?

No del todo. `CrearReserva` ahora busca reservas de la misma sala y rechaza los horarios cruzados (`lib/domain/crear_reserva.dart:16`, `lib/domain/crear_reserva.dart:18`, `lib/domain/crear_reserva.dart:23`). Las pruebas cubren los cruces, los horarios consecutivos y otras salas (`test/crear_reserva_test.dart:43`, `test/crear_reserva_test.dart:123`, `test/crear_reserva_test.dart:142`). Pero la pantalla guarda directamente en Supabase en vez de llamar a `CrearReserva` (`lib/presentation/reserva_page.dart:58`, `lib/presentation/reserva_page.dart:60`), así que puede saltarse esa regla. La base de datos tampoco bloquea esos cruces (`supabase/migracion.sql:11`, `supabase/migracion.sql:21`).
