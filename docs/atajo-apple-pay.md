# Atajo de Apple Pay → Todo en Orden

Cada vez que pagas con una tarjeta de Wallet, el Atajo deja el gasto en una «bandeja» de tu GitHub privado. La app lo trae sola (al abrirla, al guardar o en la sincronización automática) y te lo muestra en **Por confirmar**: eliges en qué fue y listo. La app recuerda «comercio → categoría» para la próxima vez.

**Necesitas:** iPhone con iOS 17 o más nuevo, tu tarjeta en Wallet (Apple Pay), y la sincronización de la app ya conectada.

> Los nombres exactos de las acciones pueden variar un poco según tu versión de iOS. No se probó en un iPhone real: pruébalo con un pago pequeño.

## 1. Crea un token solo para el Atajo
GitHub → Settings → Developer settings → Personal access tokens → Fine-grained → Generate new token.
- Nombre: `Atajo Apple Pay`. Repositorio: **solo `mi-primer-proyecto`**. Permiso: **Contents → Read and write**.
- Cópialo y **no se lo compartas a nadie** (ni a Claude). Distinto del token de la app.

## 2. Crea la automatización
Atajos → **Automatización** → **+** → **Transacción** → elige tus tarjetas → **Ejecutar inmediatamente** → siguiente → **Nuevo atajo en blanco**. Agrega estas acciones en orden:

1. **Fecha actual**.
2. **Formatear fecha** → formato personalizado `yyyy-MM-dd`. (Es la «Fecha».)
3. **Texto** con esto (toca cada variable de la **Entrada del atajo / Transacción**: Cantidad, Comercio, Tarjeta):
   `{"monto":"[Cantidad]","comercio":"[Comercio]","tarjeta":"[Tarjeta]","fecha":"[Fecha]"}`
   Deja las comillas tal cual.
4. **Codificar en Base64** (entrada: el Texto) con saltos de línea en **Ninguno**.
5. **Fecha actual** → **Formatear fecha** → formato personalizado `yyyyMMddHHmmss`.
6. **Número aleatorio** entre 1000 y 9999.
7. **Obtener contenido de URL**:
   - URL: `https://api.github.com/repos/panamanexia-spec/mi-primer-proyecto/contents/data/inbox/` + la fecha de la acción 5 + `-` + el número aleatorio + `.json`
   - Método: **PUT**
   - Encabezados: `Authorization` = `Bearer ` + tu token · `Accept` = `application/vnd.github+json`
   - Cuerpo de la solicitud: **JSON** con tres campos de tipo texto: `message` = `bandeja` · `content` = el resultado de Base64 · `branch` = `main`

## 3. Prueba
Haz un pago pequeño con Apple Pay, abre la app y mira «Por confirmar» en Inicio.

## Cosas a saber
- La automatización de Apple a veces tarda, no se activa o se activa con pagos rechazados. Por eso los gastos llegan como «Por confirmar» y no se suman solos.
- Solo funciona con pagos que Wallet registra (no efectivo).
- Si cambias o borras el token, el Atajo deja de funcionar.
