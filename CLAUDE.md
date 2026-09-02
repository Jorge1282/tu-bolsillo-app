# tu-bolsillo-app

PWA de finanzas personales para el mercado venezolano (integra tasa BCV). Monolito: todo el HTML/CSS/JS (~224 funciones) vive en un único `index.html` (~216 KB), sin bundler ni módulos separados. `manifest.json` + `sw.js` la hacen instalable como PWA; `.well-known/assetlinks.json` la habilita como TWA (app Android empaquetada).

## Dos variantes en el mismo archivo
- **Coach**: versión con insights financieros/IA.
- **Personal**: versión más simple. Se está migrando funcionalidad de Coach a Personal (cierre de mes, navegación infinita, deuda pendiente, saldo/presupuesto).

Antes de tocar una función, confirmar a cuál variante pertenece — nombres pueden repetirse o divergir entre ambas.

## Áreas funcionales (ver ADR para detalle de funciones)
Transacciones y balances · Presupuesto · Pagos/compromisos recurrentes · Categorías personalizadas · Tasa BCV (con failover) · Coach financiero · Backup cifrado a Google Drive · Cuenta/recuperación · Exportación (Excel/JSON) · Monetización (Pro).

## Persistencia
localStorage como store principal, backup opcional cifrado a Google Drive.

## Memoria estructural
Este repo está indexado en `codebase-memory-mcp` (proyecto `C-Users-osori-Documents-tu-bolsillo-app`). El grafo da poca señal a nivel de función porque todo el JS está inline en un solo HTML — para trabajar en lógica interna, usar grep dirigido por nombre de función sobre `index.html` en vez de confiar en `search_graph` para granularidad fina. El ADR completo (contexto de dominio, decisiones, trabajo reciente) está persistido ahí: consultar con `manage_adr(project="C-Users-osori-Documents-tu-bolsillo-app", mode="get")` antes de re-explorar la arquitectura desde cero.
