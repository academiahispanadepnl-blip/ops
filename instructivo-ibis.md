# Instructivo Claude Code: Cómo abrir una sesión de soporte en IBis

Cuando tengas un problema, copialo, completá los campos y pegalo al inicio de la sesión con Claude.

## Plantilla de reporte

```
PROYECTO: IBis
SUPABASE: kocwthridqvymybedttn
REPO: github.com/academiahispanadepnl-blip/apptermostatoibis

--- PROBLEMA ---

Tipo de problema (marcá uno):
[ ] Usuario no puede entrar / login
[ ] Biblioteca vacía (técnicas no aparecen)
[ ] Audio no se reproduce
[ ] Diagnóstico no funciona
[ ] Suscripción pagada pero app no la reconoce
[ ] Pantalla rota o en blanco
[ ] Otro: _______________

¿Qué pasó exactamente?
(Describí lo que ves en la pantalla, paso a paso)


¿Qué debería pasar?


¿A quién le pasa?
[ ] A todos los usuarios
[ ] Solo a usuarios nuevos
[ ] A un usuario específico: _______________ (correo)

¿Hay algún mensaje de error visible?
(Copialo textual si podés)


¿Cuándo empezó a pasar?
[ ] Siempre fue así (usuario nuevo)
[ ] Después de un cambio reciente
[ ] De repente, sin tocar nada

¿En qué dispositivo?
[ ] Celular (iOS / Android)
[ ] Computadora
[ ] Ambos
```

## Qué hace Claude con eso

Con esa info puedo ir directo al archivo correcto sin preguntarte nada extra:

| Problema | Dónde miro primero |
|---|---|
| Login roto | `auth.users`, `auth.identities`, Supabase Auth |
| Biblioteca vacía / audio no reproduce | `public.subscriptions` del usuario — casi siempre falta la fila o `status != 'active'` |
| Diagnóstico no funciona | RLS de `tecnicas`, suscripción activa, lógica de diagnóstico en frontend |
| Suscripción no reconocida | `public.subscriptions` — columnas `status`, `expires_at`, `hotmart_subscription_id` |
| Pantalla en blanco | Consola del browser, App.tsx, rutas |

## La regla de oro de IBis

Si un usuario ve la biblioteca vacía o los audios no cargan, **casi siempre es porque no tiene fila en `public.subscriptions` con `status = 'active'`**. El webhook de Hotmart a veces no llega. El fix es insertar la fila manualmente en el SQL Editor de Supabase.

## Una sola cosa extra que acelera todo

Si podés, antes de pegar la plantilla, abrí el browser en la app, hacé clic derecho → **Inspeccionar** → pestaña **Console**, y si hay texto rojo copialo también. Eso me da el error exacto en segundos.

Eso es todo. Sin ese instructivo una sesión de diagnóstico empieza a ciegas y perdemos 15 minutos reconstruyendo contexto. Con esto empezamos directo en el problema.
