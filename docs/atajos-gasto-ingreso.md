# Atajos para anotar un gasto o un ingreso sin abrir la app

Dos atajos de tu iPhone: **«Gasto»** e **«Ingreso»**. Los tocas, escribes el monto, eliges de qué es y listo: no tienes que abrir la app. Quedan guardados en tu GitHub privado y la app los trae sola la próxima vez que se sincronice (al abrirla, al guardar o cada hora, y siempre puedes tocar el botón de sincronizar).

Lo que anotes con el atajo **Gasto** sale de **Mi salario** (si quieres que salga de Dinero extra, mira «Opciones» al final). Lo que anotes con **Ingreso** es **Dinero extra**. Tu salario de clientes sigue entrando solo.

**Necesitas:** iPhone con iOS 17 o más nuevo, la app con la sincronización conectada y un token de GitHub solo para esto.

> No se probó en un iPhone real. Los nombres de las acciones pueden cambiar un poco según tu versión de iOS. Prueba con un monto pequeño y borra la prueba en la app.

## 1. El token (una sola vez)
Si ya hiciste el token del Atajo de Apple Pay, usa el mismo. Si no:

GitHub → Settings → Developer settings → Personal access tokens → Fine-grained → Generate new token.
- Nombre: `Atajos`. Repositorio: **solo `mi-primer-proyecto`**. Permiso: **Contents → Read and write**.
- Cópialo y **no se lo compartas a nadie** (ni a Claude, ni en un chat). Va solo dentro de tus atajos.

## 2. Crea el atajo «Gasto»
Atajos → **+** → nómbralo **Gasto**. Agrega estas acciones en orden:

1. **Pedir entrada** → tipo **Número** → pregunta: `¿Cuánto gastaste?`
2. **Lista** con estos elementos, uno por línea: `Casa`, `Super`, `Transporte`, `Salud`, `Ocio`, `Servicios`, `Suscripciones`, `Belleza y ropa`, `Trabajo`, `Otros`
3. **Elegir de la lista** (entrada: la Lista) → pregunta: `¿En qué?`
4. **Pedir entrada** → tipo **Texto** → pregunta: `Nota (opcional)`
5. **Fecha actual** → **Formatear fecha** → formato personalizado `yyyy-MM-dd`
6. **Texto** con esto (en cada `[ ]` toca la variable que se llama así: el Número, el elemento elegido, el texto de la nota y la fecha formateada):
   `{"tipo":"gasto","directo":"si","monto":"[Número]","categoria":"[Elemento elegido]","comercio":"[Nota]","fecha":"[Fecha]"}`
   Deja las comillas tal cual. No escribas comillas dobles dentro de la nota.
7. **Codificar en Base64** (entrada: ese Texto) → saltos de línea: **Ninguno**
8. **Fecha actual** → **Formatear fecha** → formato personalizado `yyyyMMddHHmmss`
9. **Número aleatorio** entre 1000 y 9999
10. **Obtener contenido de URL**:
    - URL: `https://api.github.com/repos/panamanexia-spec/mi-primer-proyecto/contents/data/inbox/` + la fecha del paso 8 + `-` + el número aleatorio + `.json`
    - Método: **PUT**
    - Encabezados: `Authorization` = `Bearer ` + tu token · `Accept` = `application/vnd.github+json`
    - Cuerpo de la solicitud: **JSON** con tres campos de tipo texto: `message` = `atajo` · `content` = el resultado de Base64 · `branch` = `main`
11. (Opcional) **Mostrar notificación**: `Gasto anotado`.

## 3. Crea el atajo «Ingreso»
Duplica el atajo Gasto (mantén presionado → Duplicar), nómbralo **Ingreso** y cambia:
- Paso 1: pregunta `¿Cuánto te entró?`
- Paso 2, la lista: `Dinero extra`, `Regalo`, `Venta`, `Otros ingresos`
- Paso 3: pregunta `¿De qué es?`
- Paso 6, el texto: `{"tipo":"ingreso","directo":"si","monto":"[Número]","categoria":"[Elemento elegido]","comercio":"[Nota]","fecha":"[Fecha]"}`
- Notificación: `Ingreso anotado`.

## 4. Ten los atajos a mano
Elige lo que más te guste (puedes usar varias):
- **Pantalla de inicio:** en Atajos, mantén presionado el atajo → **Detalles** → **Añadir a pantalla de inicio**. Te queda un ícono «Gasto» y otro «Ingreso» junto a la app.
- **Widget:** mantén presionada la pantalla de inicio → **+** → **Atajos** → elige el widget con tus dos atajos.
- **Tocar atrás:** Ajustes → Accesibilidad → Tocar → **Tocar atrás**: **Dos toques** = Gasto, **Tres toques** = Ingreso. Así los abres con dos golpecitos en la parte de atrás del iPhone.
- **Botón de Acción** (iPhone 15 Pro o más nuevo): Ajustes → Botón de Acción → Atajo → **Gasto**.

## Opciones
- **Si no quieres que se anote solo** y prefieres confirmarlo en la app: quita `"directo":"si",` del texto. Entonces llega a **Por confirmar** en Inicio y lo guardas con un toque.
- **Que el gasto salga de Dinero extra:** agrega `"bolsillo":"extra",` justo después de `"tipo":"gasto",`.

## Cosas a saber
- El gasto aparece en la app cuando se sincroniza, no al instante. Si quieres verlo ya, abre la app y toca el botón de sincronizar.
- Si cambias o borras el token, los atajos dejan de funcionar.
- Si compartes un atajo con alguien, **le estarías dando tu token**. No lo compartas.
- Si te equivocas, abre la app → Finanzas → Últimos movimientos y edita o borra el movimiento.
