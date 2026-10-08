# Mi Espacio: notas para continuar el trabajo en otro chat

App personal (PWA) de **agenda, trabajo con clientes, finanzas, súper y metas de ahorro**. La usa una persona que **no programa**: explica todo en español sencillo, sin jerga, y avisa qué tiene que hacer ella.

## Dónde vive todo
- **Este repositorio (`panamanexia-spec/mi-espacio`, público)**: solo la app. Todo está en `index.html` (CSS + HTML + JS en un solo archivo), más `sw.js` (service worker), `manifest.json` e íconos. **Aquí nunca van datos personales.**
- **`panamanexia-spec/mi-primer-proyecto` (privado)**: sus datos (`data/state.json`), los avisos automáticos (`tools/notify.mjs`, `.github/workflows/alertas.yml`) y las skills de diseño en `.claude/skills/` (impeccable, taste-skill, security-review, mobile-native, break-ui y otras). La app sincroniza con ese archivo desde Ajustes → Sincronización (token fine-grained de ella, guardado solo en su aparato; **nunca pedirlo ni pegarlo en el chat**).
- Los cambios se publican uniendo una rama a `main` con un pull request; la página sale de `main`.

## Cómo trabajar (flujo que se ha usado)
1. `git checkout -b claude/<tema>` desde `main` actualizado.
2. Editar `index.html`. **Subir `APP_V`** (texto de versión que se ve en Ajustes) y **la versión de caché en `sw.js`** (`mi-espacio-vN`) en cada cambio.
3. Probar con Playwright en Chromium (`/opt/pw-browsers/chromium`): abrir `file://.../index.html`, sembrar `localStorage['miespacio.v1']` con un estado de ejemplo antes de cargar (`addInitScript`), recorrer las pantallas en 390×844 y 1200×800 y vigilar `pageerror`. No hay build ni dependencias.
4. Commit, pull request, merge a `main`. Decirle a la usuaria que en ⚙️ revise la versión y use «Actualizar la app» si no coincide.

## Reglas importantes del código
- **Seguridad**: todo texto que venga de datos va con `esc()`. Hay CSP en el `<head>`. Los datos que entran (almacenamiento, respaldo, GitHub) pasan por `cleanState`/`cleanRec`: si se agrega un campo o colección nuevo hay que **añadirlo ahí** (y a `DEF`, `COLL` y al cuerpo del sync en `sync()`).
- Los ids deben cumplir `SAFE` (`/^[\w-]{1,64}$/`). Los registros generados automáticamente usan ids deterministas para no duplicarse entre aparatos (`applyRecurring`, `applyAutoPays`); lo que ella borra queda en `S.del` y no se vuelve a crear.
- Estado global `S` (events, tx, shop, clients, tasks, ideas, posts, goals, favs, del, set). `render()` repinta todo; `save()` = guardar + sincronizar.
- Diseño «Noir Orquídea»: fondo negro (oscuro por defecto; claro y automático en Ajustes), colores rosa, rojo, azul, anaranjado y violeta. **Cada sección tiene su color** (`--s-home`, `--s-agenda`, `--s-work`, `--s-money`, `--s-shop`) y **cada cliente el suyo** (`KIND`). Tipografías: Cormorant Garamond (títulos) y Manrope (texto y números). Botones ≥44 px, contraste revisado.

## Reglas de negocio acordadas con la usuaria
- **Clientes** (Trabajo): lista → una pantalla por cliente (datos y redes, pagos, tareas, publicaciones, fechas importantes, ideas). Los «proyectos» son los clientes (Nexia e Identidad Prestada vienen de inicio y se pueden editar o borrar).
- **Ingresos de un cliente**: se escribe el **monto total** (bruto si es salario) y la app reparte por periodo (`feeBase`: `mes` o `pago`; `freq`: semanal, quincenal = 15 y fin de mes, mensual). `tipo: 'planilla'` descuenta **11 %** (Seguro Social 9.75 % + Educativo 1.25 %); `tipo: 'extra'` no descuenta. El ISR no se calcula.
- **Pagos por periodo**: cada cobro lleva `for` (la fecha de pago que cubre). `periods()`/`cobro()` dicen qué está pagado, vencido, hoy o próximo. `auto: 'si'` crea el ingreso solo en cada fecha de pago; `'no'` = ella confirma cada uno.
- **En Finanzas solo se agrega a mano «Dinero extra»** (no viene de un cliente). Los pagos de clientes se registran desde su ficha. Editar un pago de un cliente abre `cobroSheet` (con opción de borrar).
- **Gastos fijos**: se repiten cada quincena, cada mes o cada 2 meses. **Metas de ahorro** con alcancía (SVG que se llena); al llegar al monto pasan solas a «Metas logradas» y se pregunta si quiere otra meta.
- Extras: buscador global, PIN de la app (privacidad por aparato, no cifra), calendario de publicaciones, sincronización automática cada hora o 4 horas (Ajustes).

## Ideas pendientes que se hablaron
Plantillas de tareas por cliente, vista semanal, recordatorios de cobro por mensaje, y pasar los datos a Supabase si quiere fotos sincronizadas (hoy las fotos viven en el aparato y se suben a GitHub en segundo plano).
