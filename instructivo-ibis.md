# Instructivo Claude Code: Cómo abrir una sesión de soporte en IBis

Cuando tengas un problema, copialo, completá los campos y pegalo al inicio de la sesión con Claude.

---

## Plantilla de reporte

```
PROYECTO: IBis
SUPABASE: kocwthridqvymybedttn
REPO: github.com/academiahispanadepnl-blip/apptermostatoibis

--- PROBLEMA ---

Tipo de problema (marcá uno):
[ ] Usuario no puede entrar / login
[ ] Biblioteca vacía (técnicas no aparecen)
[ ] Audios no se reproducen
[ ] Diagnóstico no funciona o no aparece
[ ] Suscripción pagada pero app no la reconoce
[ ] Pantalla rota o en blanco
[ ] Otro: _______________

¿Qué pasó exactamente?
(Describí lo que ves en la pantalla, paso a paso)


¿Qué debería pasar?


¿A quién le pasa?
[ ] A todos los usuarios
[ ] A un usuario específico: _______________ (correo)
[ ] Solo a usuarios nuevos
[ ] Solo a usuarios con cierto plan: _______________

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

---

## Qué hace Claude con eso

Con esa info puedo ir directo al archivo correcto sin preguntarte nada extra:

| Problema | Dónde miro primero |
|---|---|
| Login roto | `auth.users`, `auth.identities`, configuración de Supabase Auth |
| Biblioteca vacía / técnicas no aparecen | RLS de `tecnicas`, registro en `subscriptions` del usuario |
| Audios no se reproducen | Bucket de storage, URL de audio en `tecnicas`, permisos de bucket |
| Diagnóstico no funciona | Lógica de diagnóstico en el frontend, RLS, datos del usuario en Supabase |
| Suscripción no reconocida | `public.subscriptions` — columnas `status`, `expires_at`, `hotmart_subscription_id` |
| Pantalla en blanco | Consola del browser, App.tsx, rutas, error de carga inicial |

---

## La regla de oro de IBis: sin suscripción activa, la app queda vacía

La política RLS de `tecnicas` bloquea TODO el contenido si el usuario no tiene una fila en `subscriptions` con `status = 'active'` y `expires_at` en el futuro (o nulo). Aunque el usuario haya pagado en Hotmart, si el webhook no creó esa fila, ve la app vacía.

**Consulta de diagnóstico rápido** (pegar en el SQL Editor de Supabase):

```sql
-- 1. Verificar cuenta
SELECT id, email, created_at, last_sign_in_at, confirmed_at,
  raw_app_meta_data->>'providers' as providers
FROM auth.users
WHERE email = '<email del usuario>';

-- 2. Verificar suscripción
SELECT id, user_id, status, plan, hotmart_subscription_id,
  email, expires_at, created_at, updated_at
FROM public.subscriptions
WHERE email = '<email del usuario>'
   OR user_id = '<uuid del paso anterior>';

-- 3. Verificar rol
SELECT * FROM public.user_roles
WHERE user_id = '<uuid del paso anterior>';
```

Si el paso 2 no devuelve filas, o devuelve `status != 'active'`, ese es el problema.

---

## Acceso directo al SQL Editor

[Abrir SQL Editor de IBis](https://supabase.com/dashboard/project/kocwthridqvymybedttn/sql/new) *(sesión de Chrome ya autenticada)*

---

## Una sola cosa extra que acelera todo

Si podés, antes de pegar la plantilla, abrí el browser en la app, hacé clic derecho → **Inspeccionar** → pestaña **Console**, y si hay texto rojo copialo también. Eso me da el error exacto en segundos.
