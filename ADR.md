# ADR — tu-bolsillo-app

## Contexto
PWA de finanzas personales ("Tu Bolsillo") orientada al mercado venezolano (integra tasa BCV). Existen dos variantes de la app dentro del mismo código: una versión "Coach" (con IA/insights financieros) y una versión "Personal" más simple, ambas conviviendo en el mismo `index.html`.

## Decisión: monolito single-file
Todo (HTML + CSS + ~224 funciones JS) vive en un único `index.html` (~216 KB), sin build step, sin bundler, sin módulos ES separados. Se acompaña de `manifest.json` + `sw.js` (service worker) para instalación como PWA, y `.well-known/assetlinks.json` para TWA (empaquetado como app Android).

**Por qué importa para el grafo:** el indexador de codebase-memory-mcp solo ve 32 nodos (variables/módulos a nivel de archivo) porque todo el JS está inline en un HTML gigante — el parser no desglosa las ~224 funciones internas como nodos individuales. Para trabajar en lógica interna, usar `grep`/`Read` con offset sobre `index.html`, no confiar en el grafo para granularidad de función.

## Dominios funcionales identificados (por convención de nombres, no verificado línea a línea)
- **Transacciones**: `addTransaction`/`editTransaction`/`deleteTransaction`, balances (`getCurrentBalance`, `getDailyBalances`, `getMonthOpeningBalance`)
- **Presupuesto**: `getBudgetCategoryType`, `getBudgetRuleAnalysis`, `budgetEquivalentInline`
- **Pagos/compromisos recurrentes**: `addPaymentDate`, `editPaymentDate`, `getUpcomingPayments`, `checkAndNotifyPayments`, `dueUrgencyColor/Text`
- **Categorías personalizadas**: `addCustomCategory`, `deleteCustomCategory`, categorías default de gasto/ingreso
- **Tasa BCV**: `fetchBcvRate`, `fetchBcvHistory`, `getRateForDate`, con failover (`fetchWithFailover`, `fetchWithTimeout`)
- **Coach financiero**: `coachCombinedUSD`, `coachPendingCommitments`, `getCoachInsights`, `getCoachTipOfDay`, `getFinancialScore`
- **Backup/seguridad**: cifrado local (`encryptBackup`/`decryptBackup`, `deriveEncryptionKey`, `generateSalt`), integración Google Drive (`connectGoogleDrive`, `backupToDrive`, `autoBackupToDrive`, refresh de tokens)
- **Cuenta/recuperación**: `changeAccountPassword`, `changeAccountUsername`, `generateRecoveryCode`, `cancelRecovery`
- **Exportación**: `exportExcel`, `exportJSON`
- **Monetización**: `activatePro`, `dismissTrialWelcome`

## Trabajo reciente (según git log, no re-derivar)
Se está migrando/aplicando funcionalidad de la versión "Coach" a la versión "Personal": cierre de mes, navegación infinita de meses, deuda pendiente, saldo/presupuesto/saldo disponible, unificación de Pagos y Metas, unificación de moneda.

## Persistencia
localStorage como store principal (patrón "Store helper" mencionado en commit `7de0712`), con backup opcional cifrado a Google Drive.

## Implicación práctica para sesiones futuras
- No re-explorar la arquitectura desde cero: es un monolito de un archivo, dos variantes (Coach/Personal) coexistiendo.
- Confirmar en qué variante se está trabajando antes de tocar funciones (nombres a veces se repiten o divergen entre Coach/Personal).
- El grafo (search_graph/get_architecture) da poca señal aquí por ser todo inline; usar grep dirigido por nombre de función sobre index.html.
