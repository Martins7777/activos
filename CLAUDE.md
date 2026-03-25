# AMS · Gestión Patrimonial — Contexto del Proyecto

## Qué es esto
Dashboard HTML de gestión patrimonial personal. Fichero único autocontenido:
`AMS_Dashboard.html` — toda la lógica, estilos y datos viven aquí.

El Excel (`AMS_Activos_2025.xlsx`) ya **no es la fuente de verdad**. Los datos han migrado al HTML.
No referenciar ni depender del Excel en ningún cambio futuro.

## Objetivo del usuario
- **Historificar** balance y flujo de caja mes a mes mediante snapshots JSON exportados
- **Simular escenarios** comparando una posición congelada vs. la posición editada en tiempo real
- **Cargar reportes anteriores** en la pestaña Histórico para analizar evolución
- Trabajar siempre sobre el HTML directamente, con inputs editables

## Estructura del proyecto
```
activos/
├── AMS_Dashboard.html     ← fichero principal, no dividir en múltiples ficheros
├── reportes/              ← snapshots JSON exportados (reporte_YYYY-MM-DD.json)
│   └── LEEME.txt
└── CLAUDE.md
```

## Pestañas actuales
| Pestaña | Función |
|---|---|
| Resumen | KPIs, gráficas de composición y flujo, ratios rápidos |
| Posición | Flujo de caja (arriba) + Balance (abajo) — todos los campos editables |
| Ratios | 10 ratios patrimoniales con semáforos y barras vs. objetivo |
| Simulador | Comparativa Base congelado vs. Simulado en tiempo real |
| Histórico | Carga múltiples JSON y muestra evolución con tablas y gráficas |

## Datos base (valores migrados del Excel)
Están definidos en `const BASE = {...}` al inicio del `<script>`.
Cuando el usuario pide actualizar un valor concreto, editar directamente ese campo en BASE
y también el `value` del input HTML correspondiente.

## Filosofía de desarrollo — MUY IMPORTANTE
- **Simplicidad sobre complejidad**: una tabla clara vale más que un cálculo elaborado
- **Rapidez de navegación**: no añadir librerías pesadas, animaciones largas ni modales innecesarios
- **Sin sobrecarga visual**: cada elemento que se añade debe justificarse
- **Fichero único**: no fragmentar en CSS/JS externos. Todo en el HTML
- **No romper lo existente**: cada cambio es quirúrgico. Editar solo lo pedido
- **Inputs editables en Posición**: todo valor de ingreso, gasto, activo y pasivo debe ser editable
- **Recálculo inmediato**: cualquier cambio de input dispara `recalcAll()` que actualiza toda la UI

## Ratios que se calculan (todos dinámicos)
- Tasa de esfuerzo oficial: cuotas / ingresos base (sin bonus). Límite < 35%
- Tasa de esfuerzo real: (cuotas + gastos activos) / ingresos base. Límite < 40%
- Ratio de endeudamiento: pasivos / activos. Límite < 70%
- Apalancamiento: activos / patrimonio neto. Límite < 3x
- Deuda / ingresos anuales: pasivos / (ingresos × 12). Límite < 5x
- Colchón de liquidez: (fondos + santander) / gasto mensual. Objetivo > 6x
- Tasa de ahorro: ahorro / ingresos. Objetivo > 20%
- Yield bruto inmobiliario: rentas anuales / valor inmuebles. Objetivo > 4%
- Diversificación: inmobiliario / activos totales. Límite < 60%
- Activos financieros / patrimonio neto. Objetivo > 30%

## Formato de snapshot JSON (reportes/)
Cada exportación genera `reporte_YYYY-MM-DD.json` con:
- Fecha, totales de flujo, balance, ratios calculados
- Detalle de cada bloque (ingresos, gastos, activos, pasivos)
- Se carga en pestaña Histórico para ver evolución

## Qué NO hacer
- No añadir dependencias externas salvo Chart.js (ya incluido) y html2canvas si se pide JPG
- No crear ficheros CSS o JS separados
- No referenciar el Excel en ninguna funcionalidad nueva
- No añadir formularios complejos, modales, ni animaciones pesadas
- No recalcular ratios con fórmulas distintas a las ya definidas sin consultar
- No modificar el aspecto visual ni la estructura de pestañas sin que se pida explícitamente
