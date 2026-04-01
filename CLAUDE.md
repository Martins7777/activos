# index.html — Referencia de Edición

**Total líneas: 509** | **Última actualización: 2026-04-01** | **Modelo: Haiku**

---

## 🎨 CSS — ESTILOS (L9-277)

### Paleta de Colores (L10-18)
```
--bg: #f8fafc          (fondo body)
--brand: #1a2c4e       (headers, acentos)
--text: #1e293b        (texto principal)
--text-muted: #64748b  (etiquetas tenues)
--border: #e2e8f0      (bordes)
--pos: #10b981         (verde ingresos/activos)
--neg: #ef4444         (rojo gastos/pasivos)
```

### Secciones de Estilos
| Sección | Líneas | Propósito |
|---|---|---|
| Fuente & Reset | L21-28 | Globales |
| Header | L31-42 | Barra superior sticky |
| KPIs superiores | L46-61 | `.summary-grid`, `.stat-card`, `.stat-val` |
| Grid cards | L63-69 | 2 columnas: `1fr 1fr` (desktop), 1fr (<640px) |
| Card header | L71-88 | `.card-header` con título total |
| Subsecciones | L90-118 | `.group-title-row` (fondo #f1f5f9) + `.group-title-input` |
| **Fila de item (flexbox)** | L120-141 | `.row-item` + `.col-concept/tag/val/del` |
| Inputs texto | L143-156 | `white-space:nowrap`, `text-overflow:ellipsis` |
| Valores display | L158-172 | `.val-display` (span clickeable) + `.euro-icon` |
| Valores input | L174-187 | `.val-input` (bg #fffdeb, border activo) |
| Tags badge | L192-210 | `.tag-select` (9px uppercase) |
| Botones | L212-270 | `.btn-del`, `.btn-add-item`, `.btn-add-group`, `.btn-io` |
| Responsive | L272-276 | `@media (max-width:640px)` |

**Columnas flexbox (críticas):**
- `.col-concept`: `flex:1, min-width:0` → absorbe espacio
- `.col-tag`: `width:64px` (56px mobile)
- `.col-val`: `width:82px` (76px mobile)
- `.col-del`: `width:22px` → siempre visible

---

## 📱 HTML — ESTRUCTURA (L279-309)

| Parte | Líneas | ID/Clase |
|---|---|---|
| **Header** | L281-289 | Botones: `↓ Exportar`, `↑ Importar`, `#date` |
| **KPIs** | L292-301 | `#neto-total`, `#ahorro-total` |
| **Grid** | L303-308 | `#container-ingresos/gastos/activos/pasivos` |

Nota: Los inputs `#fileInput` y el contenido de cards se generan dinámicamente en `render()`.

---

## 💾 STATE — DATOS (L312-360)

**Estructura inmutable:**
```js
state = {
  ingresos: [ { title, items: [{label, type, value}] } ],
  gastos:   [ ... ],
  activos:  [ ... ],
  pasivos:  [ ... ]
}
```

**Datos iniciales:**
- `ingresos` (L313-322): Trabajo, Rentas — 5 items
- `gastos` (L324-332): Vivienda+Deuda, Personales — 4 items
- `activos` (L334-344): Inmobiliario, Financieros — 6 items
- `pasivos` (L346-351): Hipotecas — 3 items

**Tags por categoría (L355-360):**
```
ingresos: Trabajo, Rentas, Variable, Otros
gastos:   Vivienda, Deuda, Propiedades, Personal, Ahorro, Impuestos
activos:  Inmobiliario, Financiero, Liquidez, Otros
pasivos:  Hipoteca, Préstamo, Tarjeta, Otros
```

---

## ⚙️ FUNCIONES — JS (L362-506)

| Función | Líneas | ¿Qué hace? |
|---|---|---|
| `fmt(v)` | L362-363 | Formatea número con separador (25000 → "25.000 €") |
| `fmtNum(v)` | L365-366 | Igual, sin símbolo (25000 → "25.000") |
| **`render()`** | **L369-438** | **Reconstruye TODO el DOM desde `state`** |
| `editVal(span, c, g, i)` | L441-454 | Click-to-edit: reemplaza span por input numérico |
| `update(c, g, i, f, v)` | L456-459 | Muta `state[cat][gIdx].items[iIdx][field]` + render |
| `addItem(c, g)` | L461-464 | Push nuevo concepto a un grupo |
| `remove(c, g, i)` | L466-469 | Splice concepto |
| `addGroup(c)` | L471-474 | Push nuevo grupo (sección) a una categoría |
| `exportData()` | L476-483 | Descarga state como JSON (ams_patrimonio_YYYY-MM-DD.json) |
| `importData(input)` | L485-502 | Lee JSON, valida 4 secciones, reemplaza state |
| Init | L504-506 | Carga fecha + llama render() |

---

## 🔧 PATRONES DE EDICIÓN

### Cambiar valores CSS fácilmente
```
Paleta:        Edita L10-18 (--brand, --pos, --neg, etc.)
Tipografía:    Busca "font-size: Xpx" → Edit directo
Espaciado:     Busca "padding:", "gap:", "margin:" → Edit directo
Grid layout:   L64-68 (grid-template-columns), L272-276 (@media)
```

### Cambiar estructura de datos
```
State inicial:  L312-353 (estado por defecto)
Tags:           L355-360 (agregar/quitar opciones de select)
Editar valores: Cambiar .value directamente en L314-351
```

### Cambiar comportamiento de funciones
```
render():           L369-438 (template del HTML dinámico)
editVal():          L441-454 (lógica click-to-edit)
import/export:      L476-502 (validación JSON)
```

### Cambiar estilos de elementos específicos
```
Headers:        Busca ".card-header" (L78-88)
Subsecciones:   Busca ".group-title-row" (L91-98)
Filas items:    Busca ".row-item" (L121-128)
Botones:        Busca ".btn-" (L212-270)
Inputs focus:   Busca ":focus" (L111, 156, 187, 210)
```

---

## 📊 FLUJO DE RENDERIZADO

1. **Mutation** → `update()`, `addItem()`, `remove()`, `addGroup()`
2. **Trigger** → Llamada a `render()`
3. **render()** itera `['ingresos', 'gastos', 'activos', 'pasivos']` (L372)
4. Para cada categoría: itera grupos (L384) → itera items (L394)
5. Construye HTML strings (template literals L378-423)
6. **setInnerHTML** en `container-{cat}` (L424)
7. Actualiza KPIs: `#neto-total`, `#ahorro-total` (L429-437)

---

## ⚡ REGLAS CRÍTICAS

1. **Nunca** cambiar estructura de `state` sin actualizar `render()` y `importData()`
2. `render()` **destruye y recrea** todo → onclick handlers inline aún funcionan
3. Click-to-edit (`editVal`) **reemplaza temporalmente** el span por input (L448)
4. `fmt()` usa **es-ES locale** → separador miles = punto (1.000, 1.210, etc.)
5. Grid: `2 columnas` desktop, `1 columna` mobile (<640px)
6. **Sin saltos de línea**: `white-space:nowrap` en `.val-display`, `.stat-val`, `.col-tag`
7. Anchos fijos px (tag/val/del) → NO porcentajes → evita desbordamiento

---

## 🚀 GREP RÁPIDO

```bash
# Buscar donde se usa categoría específica
grep -n "container-ingresos\|gastos\|activos\|pasivos" index.html

# Encontrar listeners onclick
grep -n "onclick=" index.html

# Listar todas las clases CSS
grep -n "^\s*\." index.html | head -30

# Encontrar una función
grep -n "^  function " index.html
```

---

**Próximas ediciones: Usa esta referencia. NO releas el archivo completo.**
