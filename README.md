# Fleet Manager — Nehuel & Co.

App web de gestión de flota vehicular. Hosteada en GitHub Pages, con Google Sheets como base de datos vía Google Apps Script.

---

## Estructura del proyecto

```
fleet-manager/
├── index.html   ← App completa (HTML + CSS + JS, un solo archivo)
└── Code.gs      ← Backend en Google Apps Script (no va al repo de GitHub)
```

---

## Setup inicial (una sola vez)

### 1. Google Sheets + Apps Script

1. Creá una **Google Sheets** nueva y nombrala `Fleet Manager`.
2. En el menú, andá a **Extensiones → Apps Script**.
3. Borrá el contenido de `Code.gs` y pegá el contenido del archivo `Code.gs` de este proyecto.
4. Guardá con `Ctrl+S`.
5. En el menú **Ejecutar**, elegí la función `initSheets` y ejecutala. Esto crea las dos hojas con los encabezados correctos:
   - **Vehículos**: `id | nombre | marca | modelo | patente | anio | km | kmProxService | estado | sede | taller | fechaAdq | notas | fechaAlta`
   - **Registros**: `id | vehiculoId | tipo | fecha | km | descripcion | resuelto | fechaResolucion | taller`
6. Andá a **Implementar → Nueva implementación**:
   - Tipo: **Aplicación web**
   - Ejecutar como: **Yo**
   - Acceso: **Cualquier usuario**
7. Copiá la **URL de implementación** generada (empieza con `https://script.google.com/macros/s/...`).

### 2. GitHub Pages

1. Creá un **repositorio público** en GitHub.
2. Subí únicamente el archivo `index.html`.
3. Andá a **Settings → Pages** → Source: `Deploy from a branch` → `main / root`.
4. Tu app queda disponible en `https://tuusuario.github.io/nombre-repo`.

### 3. Conectar la app

1. Abrí la URL de GitHub Pages.
2. La app te lleva directo a **Configuración**.
3. Pegá la URL del Apps Script y hacé clic en **Guardar y conectar**.

---

## Esquema de datos

### Hoja: Vehículos (14 columnas)

| # | Campo | Descripción |
|---|---|---|
| 1 | id | UUID corto, generado automáticamente |
| 2 | nombre | Marca + Modelo concatenados |
| 3 | marca | Ej: Ford |
| 4 | modelo | Ej: Ranger 2.2 |
| 5 | patente | Uppercase |
| 6 | anio | Año del vehículo |
| 7 | km | Kilometraje actual |
| 8 | kmProxService | Km para próximo service |
| 9 | estado | `ok` / `warn` / `bad` / vacío (automático) |
| 10 | sede | SAN MARTIN / MENDOZA / etc |
| 11 | taller | Taller habitual |
| 12 | fechaAdq | Fecha de adquisición |
| 13 | notas | Observaciones libres |
| 14 | fechaAlta | Fecha de alta en el sistema |

### Hoja: Registros (9 columnas)

| # | Campo | Descripción |
|---|---|---|
| 1 | id | UUID corto |
| 2 | vehiculoId | FK → Vehículos.id |
| 3 | tipo | `service` / `averia` / `revision` / `combustible` / `km` / `otro` |
| 4 | fecha | Fecha del evento |
| 5 | km | Km del vehículo al momento |
| 6 | descripcion | Detalle libre |
| 7 | resuelto | `SI` / `NO` |
| 8 | fechaResolucion | Solo para averías resueltas |
| 9 | taller | Taller donde se realizó |

---

## Lógica de estado automático

Si `estado` está vacío o es `ok`, el frontend calcula el estado dinámicamente:

- **`bad`** → tiene averías pendientes, o el km actual superó el kmProxService
- **`warn`** → quedan menos de 3.000 km para el próximo service
- **`ok`** → todo en orden

Si `estado` es `warn` o `bad` (fijado manualmente), ese valor tiene prioridad.

---

## Re-deployar cambios en Apps Script

Cada vez que modifiques el `Code.gs`:

1. **Implementar → Administrar implementaciones**
2. Hacé clic en el lápiz ✎ de la implementación activa
3. En "Versión" elegí **Nueva versión** → Guardá

La URL no cambia al crear nueva versión.
