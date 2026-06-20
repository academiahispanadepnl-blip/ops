# Instructivo Claude Code: Cómo abrir una sesión de soporte en IBis

Cuando tengas un problema, copialo, completá los campos y pegalo al inicio de la sesión con Claude.

---

## Plantilla de reporte

```
PROYECTO: IBis (App Termostato)
SUPABASE: [ID del proyecto Supabase]
REPO: github.com/academiahispanadepnl-blip/apptermostatoibis

--- PROBLEMA ---

Tipo de problema (marcá uno):
[ ] El termostato no conecta / no responde
[ ] La temperatura no se actualiza o muestra datos incorrectos
[ ] No puedo entrar / login
[ ] Los cambios de configuración no se guardan
[ ] Pantalla rota o en blanco
[ ] Notificaciones no llegan
[ ] Otro: _______________

¿Qué pasó exactamente?
(Describí lo que ves en la pantalla, paso a paso)


¿Qué debería pasar?


¿A quién le pasa?
[ ] A todos los usuarios
[ ] Solo a un usuario específico: _______________
[ ] Solo en un dispositivo: _______________

¿Hay algún mensaje de error visible?
(Copialo textual si podés)


¿Cuándo empezó a pasar?
[ ] Siempre fue así
[ ] Después de un cambio reciente
[ ] De repente, sin tocar nada

¿En qué dispositivo?
[ ] Celular (iOS / Android)
[ ] Computadora / tablet
[ ] Ambos
```

---

## Qué hace Claude con eso

Con esa info puedo ir directo al archivo correcto sin preguntarte nada extra:

| Problema | Dónde miro primero |
|---|---|
| Termostato no conecta | Lógica de conexión al dispositivo, cliente WebSocket/MQTT, config de red |
| Temperatura no actualiza | Polling/suscripción en tiempo real, tabla de lecturas en Supabase, RLS |
| Login roto | Configuración de Supabase Auth, hook de sesión, rutas protegidas |
| Configuración no guarda | Tabla de settings en Supabase, RLS, función de guardado |
| Pantalla en blanco | Consola del browser, App.tsx, rutas, errores de carga inicial |
| Notificaciones | Configuración de push notifications, triggers en Supabase |

---

## Una sola cosa extra que acelera todo

Si podés, antes de pegar la plantilla, abrí el browser en la app, hacé clic derecho → **Inspeccionar** → pestaña **Console**, y si hay texto rojo copialo también. Eso me da el error exacto en segundos.

Si el problema es con el **termostato físico**, también sirve copiar cualquier log o código de error que muestre el dispositivo o la app al intentar conectar.
