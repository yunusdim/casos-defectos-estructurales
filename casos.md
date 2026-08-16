# Casos — defectos estructurales reportados

Corte: 2026-08-16 (actualizado tras movimiento en 6 casos: 4 cierres nuevos y 2 respuestas sustantivas de mantenedor). Ledger de todos los casos donde participamos: qué se planteó, qué aportamos, qué pasó, si hay historia o no. Verificado contra el estado real de cada issue/discussion/reporte, no contra memoria de sesión.

Resumen: 31 issues de GitHub + 1 comentario en discussion + 1 reporte MSRC = 33 casos (+ 1 intento bloqueado, mypy-baseline, no cuenta como caso). 7 cerrados: 3 con comentario del mantenedor (phpstan #15078 completed tras respuesta sustantiva, golangci-lint #6714 declined, deptry #1654 not-planned con "AI slop") y 4 sin ningún comentario (dependency-check #8751, pip-audit #1113 y diff_cover #619, todos not-planned/completed mudos, más Hypothesis #4854 cerrado por política de AI). 7 con intercambio real con mantenedor o colaborador — el más fuerte, SwiftLint #6871, ya tiene PR de fix abierta por un tercero; jscpd #938 tiene un diseño completo de baseline aceptado por el mantenedor (kucherenko); eslint #21223 tiene una pregunta de clarificación del mantenedor (mdjermanovic) ya respondida. MSRC en Review/Repro (cola activa, sin mensaje humano aún). El resto, sin respuesta todavía.

## Casos 23-31 · segundo rastrillaje (6 categorías nuevas: secret scanning, type-checker baselines, dead-code baselines, duplicate-code detectors, dependency-vuln suppression, quality gates de código nuevo)

Filed con el mismo registro de sala terse. Metodología: 6 agentes en paralelo, 24 herramientas evaluadas, código fuente real leído en cada caso, repro en vivo priorizado sobre code-only. 9 de 10 candidatos con repro en vivo se llegaron a filear — el décimo, **mypy-baseline** (orsinium-labs/mypy-baseline), tiene "Issue creation is restricted in this repository" y no tiene Discussions habilitadas: vulnerable confirmado con repro en vivo (preserve_position=False por default permite swap de mismo mensaje en otra línea del mismo archivo, invisible), pero sin canal público para reportarlo. Queda documentado acá sin issue.

Limpias/no aplican en este segundo rastrillaje (9): detect-secrets, git-secrets, trufflehog, pyright, Pyre, typescript-strict-plugin, ts-prune, PMD CPD (núcleo), Code Climate (discontinuado, absorbido por Qlty). Vulnerables solo-documentación, no fileadas por baja confianza: SonarQube (coverage/duplicación de "new code", no los issues discretos — esos sí trackean identidad), Codacy (Diff Coverage), Qlty (Diff Coverage) — los tres SaaS o motor de gate cerrado.

| # | Caso | Estado | Evidencia |
|---|------|--------|-----------|
| 23 | gitleaks/gitleaks [#2239](https://github.com/gitleaks/gitleaks/issues/2239) | Open | En vivo — `.gitleaksignore` fingerprint `file:rule:line` sin contenido del secreto |
| — | orsinium-labs/mypy-baseline | **Bloqueado, sin issue** | En vivo confirmado (`preserve_position=False` default), pero issue creation restringida y sin Discussions |
| 24 | jendrikseipp/vulture [#430](https://github.com/jendrikseipp/vulture/issues/430) | Open | En vivo — whitelist matchea por nombre de símbolo puro, sin archivo/línea |
| 25 | webpro-nl/knip [#1949](https://github.com/webpro-nl/knip/issues/1949) | Open | En vivo — `--max-issues` gatea por count agregado |
| 26 | osprey-oss/deptry (ex fpgmaas/deptry) [#1654](https://github.com/fpgmaas/deptry/issues/1654) | **Closed (not planned)** | En vivo — `--per-rule-ignores` matchea por nombre de módulo puro (DEP001/003/004). mkniewallner cerró con "Yeah this is AI slop, closing." Respondimos con tono neutro señalando que el repro y el gap siguen ahí independiente del registro. Sin reapertura |
| 27 | kucherenko/jscpd [#938](https://github.com/kucherenko/jscpd/issues/938) | Open | En vivo — `ThresholdReporter` solo mira % agregado. **kucherenko aceptó el planteo**: diseño completo de modo baseline opt-in (`.jscpd-baseline.json`, fingerprint por contenido+ruta, no por línea), a implementar en el motor Rust v5 |
| 28 | quandarypeak/simian [#23](https://github.com/quandarypeak/simian/issues/23) | Open | En vivo — `Checker.check()` gatea por count/% agregado sin ubicación |
| 29 | IBM/audit-ci [#356](https://github.com/IBM/audit-ci/issues/356) | Open | En vivo — allowlist por GHSA-ID bare, sin scoping de paquete/versión por default |
| 30 | pypa/pip-audit [#1113](https://github.com/pypa/pip-audit/issues/1113) | **Closed (not planned)** | En vivo — `--ignore-vuln` global sin scoping de paquete/versión. Cerrado por William Woodruff sin ningún comentario. Sin reapertura |
| 31 | pyupio/safety [#907](https://github.com/pyupio/safety/issues/907) | Open | En vivo — `ignore-vulnerabilities` por ID puro, sin binding a paquete/versión, sin expiry por default |
| 32 | apache/maven-pmd-plugin [#724](https://github.com/apache/maven-pmd-plugin/issues/724) | Open | Solo código (declarado explícitamente — bloqueo de red a Maven Central) — `excludeFromFailureFile` excluye por par-de-archivos completo, `maxAllowedViolations` es count puro |
| 33 | dependency-check/DependencyCheck [#8751](https://github.com/dependency-check/DependencyCheck/issues/8751) | **Closed (not planned)** | Solo código (declarado explícitamente — build+NVD feed inviable) — regla `<cve>` sin cpe/gav/purl/sha1 matchea cualquier dependencia con ese CVE. Nota: el repo original `jeremylong/DependencyCheck` está archivado desde sep-2025; filed en el fork activo `dependency-check/DependencyCheck`. Cerrado por chadlwilson sin ningún comentario. Sin reapertura |

Movimiento en este bloque (16-ago): deptry #1654, pip-audit #1113 y dependency-check #8751 cerrados — ver detalle en cada fila. jscpd #938 sigue abierto pero con diseño aceptado por el mantenedor. El resto sin respuesta todavía.

## Casos 16-22 · rastrillaje sistemático (6 categorías nuevas), registro de sala nuevo

Filed con el prompt de registro de sala (repro primero, sin marco académico, cita de fuente solo al final como antecedente opcional) tras un rastrillaje de 24 herramientas en SAST, IaC, coverage gates, test flake quarantine, policy engines y accesibilidad, hecho con 6 agentes en paralelo. 18 de las 24 salieron limpias o no aplican (CodeQL, Snyk, Trivy, Grype, tfsec, KICS, terrascan, nyc, coverage.py, pytest, Jest, Buildkite, CircleCI, axe-core, eslint-plugin-jsx-a11y, y en el dominio de policy engines: OPA/Rego y Cedar limpios por documentación honesta, Kyverno con bugs ya parcheados, Gatekeeper con fail-open documentado por el propio proyecto — ninguno de estos generó issue).

| # | Caso | Estado | Evidencia |
|---|------|--------|-----------|
| 16 | PyCQA/bandit [#1467](https://github.com/PyCQA/bandit/issues/1467) | Open | En vivo — baseline por `Issue.__eq__` sin `lineno`, matching por pertenencia no multiplicidad, absorbe N hallazgos sin límite |
| 17 | bridgecrewio/checkov [#7647](https://github.com/bridgecrewio/checkov/issues/7647) | Open | En vivo — baseline matchea por resource+check_id ignorando el archivo, colapso cross-file |
| 18 | Bachmann1234/diff_cover [#619](https://github.com/Bachmann1234/diff_cover/issues/619) | **Closed (completed)** | En vivo — `total_percent_covered()` agrega todo el diff, bug real diluido por líneas triviales. Cerrado por Bachmann1234 sin comentario ni PR/commit vinculado — no se sabe si lo consideró resuelto o simplemente lo cerró |
| 19 | pa11y/pa11y-ci [#352](https://github.com/pa11y/pa11y-ci/issues/352) | Open | En vivo — threshold por página entera, todas las reglas mezcladas |
| 20 | GoogleChrome/lighthouse-ci [#1145](https://github.com/GoogleChrome/lighthouse-ci/issues/1145) | Open | En vivo — `maxLength` cuenta items del audit sin comparar selector |
| 21 | justjake/eslint-seatbelt [#30](https://github.com/justjake/eslint-seatbelt/issues/30) | Open | Solo código (declarado explícitamente en el issue) — hereda el patrón de ESLint bulk suppressions a grano más fino |
| 22 | codecov/umbrella [#1989](https://github.com/codecov/umbrella/issues/1989) | Open | Solo código (SaaS, declarado explícitamente) — mismo patrón que diff_cover, target "auto" lo hace explotable sin configuración especial |

Movimiento en este bloque (16-ago): diff_cover #619 cerrado (completed, sin comentario ni PR/commit vinculado). El resto sigue sin respuesta.

---

## 1 · TNG/ArchUnit #1700

**Link**: https://github.com/TNG/ArchUnit/issues/1700
**Estado**: Open. Sin historia — 0 comentarios.

Qué planteamos: `FreezingArchRule` está bien diseñado (absorbe violación puntual, no permite la clase — no degenera como el criterio de separación genérico predice). El gap: no expone el tamaño del store congelado ni su delta. Un build verde con el store creciendo 40 líneas y uno con el store bajando 40 son indistinguibles desde afuera. Pedimos exponer store size / delta.

Qué pasó: nada aún.

---

## 2 · sverweij/dependency-cruiser #1078

**Link**: https://github.com/sverweij/dependency-cruiser/issues/1078
**Estado**: Open. Sin historia — 0 comentarios.

Qué planteamos: el known-violations file hace bien la granularidad (por instancia, no por regla), y ya imprime el count en cada corrida verde — mejor que la mayoría. Pero el count es nivel, no cambio: `⚠ 20 known violations ignored` es igual si el store creció de 12 a 20 o bajó de 28 a 20. Pedimos delta contra el archivo en disco, y/o modo shrink-only para `depcruise-baseline`.

Qué pasó: nada aún.

---

## 3 · seddonym/import-linter #375

**Link**: https://github.com/seddonym/import-linter/issues/375
**Estado**: Open. Sin historia — 0 comentarios.

Qué planteamos: `ignore_imports` con entradas literales está bien (absorción por instancia) y `unmatched_ignore_imports_alerting` (default error) fuerza a limpiar excepciones muertas — ambos correctos. El gap: los wildcards (`**`) rompen ambas garantías a la vez, permiten una clase entera y quedan "matched" mientras el conjunto que cubren crece de 1 a 40 sin que nada alerte. Pedimos trackear match count por expresión wildcard y alertar cuando crece.

Qué pasó: nada aún.

---

## 4 · openrewrite/rewrite #8498

**Link**: https://github.com/openrewrite/rewrite/issues/8498
**Estado**: Open. Sin historia — 0 comentarios de mantenedores (solo bot de project automation agregándolo al board).

Qué planteamos: `runRecipeCycles` sale por tres caminos (fixpoint, panic, budget agotado) y los tres devuelven el mismo `RecipeRun`, sin distinguir cuál fue. Argumentamos con nuestro propio trabajo en reparación estructural iterada: la salida por budget agotado no es un resultado, es un artefacto de control — y citamos un issue previo del propio repo (#2415) cerrado con una respuesta que trata ambas salidas como equivalentes. Pedimos exponer cycle count y/o razón de terminación.

Qué pasó: nada aún, más allá del bot de triage.

---

## 5 · HypothesisWorks/hypothesis #4854 — CERRADO

**Link**: https://github.com/HypothesisWorks/hypothesis/issues/4854
**Estado**: **Closed** (por el mantenedor).

Qué planteamos: sugerencia de documentación — un property test que lee su umbral desde el módulo bajo test es tautológico (no puede fallar aunque el umbral esté mal), y property-based testing empuja exactamente hacia esa forma por buenas prácticas (no duplicar el literal). Con medición propia: 17 de 19 reglas de una suite siguieron en verde con una constante rota a propósito. Propusimos la regla del "un ancla": un solo test que fija el valor literal, el resto referencia la constante real.

Qué pasó — con historia real:
- **Liam-DeVoe** (mantenedor) cerró el issue citando la política de uso de AI del repo (CONTRIBUTING.rst#ai-policy) — el issue no tenía el disclosure requerido de asistencia por IA.
- Respondimos reconociendo el error explícitamente ("Fair, and my mistake"), aclaramos los términos que la política pide (dirección de la investigación, verificación de fuentes, responsabilidad del contenido), notificamos que agregamos disclosure a los demás issues abiertos, y ofrecimos reescribirlo más corto y en palabras propias si el maintainer lo prefería.
- Sin respuesta posterior. Issue permanece cerrado, sin reapertura.

---

## 6 · github/spec-kit #4106 — el caso con más interacción

**Link**: https://github.com/github/spec-kit/issues/4106
**Estado**: Open. Colaboración activa, en curso.

Qué planteamos: `/speckit.analyze` no detecta cuando un vocabulario cerrado (un set de valores admisibles) se enumera distinto en distintas partes de una spec — ni la verificación estructural ni el oráculo de trayectorias lo cubren. Con un caso medido propio: un sistema de 40 módulos generado por contrato, 4 defectos sobrevivieron verificación completa, los 4 eran la misma clase de error (transcripción de una definición), uno de ellos exactamente este.

Qué pasó — historia sustancial:
- **mnriem** (colaborador) respondió pidiendo que se construya primero como preset para ver cómo funciona en la práctica.
- Aceptamos, preguntamos sobre la forma correcta de extender un comando core sin tener que mantener una copia completa. mnriem aclaró: los presets soportan append/prepend/wrap, no solo replace.
- Publicamos una **corrección propia** sobre el caso motivador original (el párrafo que describía el defecto estaba mal — las dos enumeraciones del contrato entregado en realidad coincidían, la que difería era un estado intermedio de la corrida, no el artefacto final) — con errata publicada en Zenodo (DOI 10.5281/zenodo.21926137), y honestamente señalamos qué le cuesta esto al pedido original (el check propuesto no habría atrapado ese caso específico).
- Aportamos un caso real distinto encontrado con un checker propio corriendo sobre 6 contratos propios (553 declaraciones), con una divergencia real de 4 vs 6 miembros del mismo set.
- Construimos el preset (append, no copy), con 3 fixtures (1 positivo, 2 controles negativos), reporte de coverage obligatorio, y honestidad explícita sobre 3 corridas del check contra sus propias fixtures donde 2 de 3 dieron resultado correcto por razón equivocada (grouping fallaba silenciosamente).
- **mnriem**: "Solid — appreciate the correction, and the 4-vs-6 case is squarely what this is for. Looking forward to the preset." — feedback positivo explícito.
- Reportamos medición de campo sobre 11 contratos reales propios: 5 no aplican, 6 con 303 declaraciones inspeccionadas, 44 reconocidas (15%), 0 divergencias — con la limitación declarada de que sabemos que hay una divergencia real en ese corpus que el check no puede alcanzar (es contra la fuente, no entre declaraciones).
- Issue sigue abierto. Pendiente: correr `/speckit-analyze` end-to-end con un agente real (declarado explícitamente como no hecho todavía).

---

## 7 · eslint/eslint #21223

**Link**: https://github.com/eslint/eslint/issues/21223
**Estado**: Open. Con historia — pregunta de clarificación del mantenedor, ya respondida.

Qué planteamos: distinguimos este pedido de dos issues previos relacionados y cerrados (#19321 cerrado como duplicado de #17609; #17609 resuelto por detección de fixes circulares en v9.23.0) — argumentamos que la detección circular no cubre el caso real: salida silenciosa por `MAX_AUTOFIX_PASSES` agotado con fixes pendientes. Mismo argumento de fondo que en openrewrite: presupuesto agotado no es un resultado, es un control.

Qué pasó — verificado en vivo el 2026-08-16:
- **mdjermanovic** (mantenedor) respondió citando nuestra propia línea del pedido y preguntando: *"Can you please clarify the request? Is it to emit a warning (`process.emitWarning()`) when fixable errors remain after 10 fix passes for a file?"*
- Confirmamos: *"Yes, exactly that — emit a warning (or equivalent) when the loop exits at MAX_AUTOFIX_PASSES with fixable errors still remaining, distinct from the existing circular-fix warning."*
- Issue sigue abierto, a la espera de que el mantenedor avance con la implementación o dé el siguiente paso.

---

## 8 · phpstan/phpstan #15078 — CERRADO, de la respuesta más hostil a cierre técnico

**Link**: https://github.com/phpstan/phpstan/issues/15078
**Estado**: **Closed** (completed, por el mantenedor).

Qué planteamos: `ignoreErrors` de PHPStan es más fuerte que la mayoría de baselines comparables — bounded en ambas direcciones (más ocurrencias de las registradas, falla; menos, también falla con `reportUnmatchedIgnoredErrors`). El gap está adentro de un mismo bucket `(path, message-regex) → count`: reproducido, arreglamos una violación real e introdujimos otra distinta del mismo tipo en el mismo archivo, el count se mantuvo en 2, `[OK] No errors`.

Qué pasó — con historia real, verificado en vivo el 2026-08-15:
- **ondrejmirtes** (mantenedor) respondió primero: *"I'm sorry, this is unreadable AI slop. Please tell me in plain words what are you asking."*
- Respondimos en términos llanos, reformulando la afirmación en una frase, y señalamos directamente que el tono no correspondía a cómo se abre una respuesta a un reporte, sin escalar ni disculparse de más.
- **ondrejmirtes** volvió con una respuesta sustantiva: *"The current granularity is a good compromise which works pretty well. Finer granularity would require tracking exact lines and recompute them based on Git diffs. There are projects that do that, like https://github.com/DaveLiddament/sarb."* — reconoce el gap, lo justifica como trade-off deliberado, y señala una herramienta externa (sarb) que sí trackea por línea vía diffs de Git.
- Cerrado como completed. Sin reapertura.

---

## 9 · rubocop/rubocop #15570

**Link**: https://github.com/rubocop/rubocop/issues/15570
**Estado**: Open. Sin historia — 0 comentarios.

Qué planteamos: `TodoAudit#offending_cops_without_todo` filtra por `File.file?`, que es `false` para un string con glob — así que cualquier entrada `Exclude` con wildcard (`**/*_spec.rb`, típico en cops de RSpec generadas automáticamente) nunca entra al set auditado, nunca se marca como stale. Verificado con búsqueda de código: ~6.300 repos con este patrón, incluyendo proyectos grandes (mongodb/mongoid, gocardless/statesman). Pedimos expandir los globs antes del filtro.

Qué pasó: nada aún.

---

## 10 · eslint/eslint #21226 — el caso original de la línea de trabajo

**Link**: https://github.com/eslint/eslint/issues/21226
**Estado**: Open. Sin respuesta directa de mantenedores — solo etiquetado automático (`core`, `enhancement`) y bot de triage.

Qué planteamos: `eslint-suppressions.json` bucketiza por `(file, rule) → count`, no por identidad de violación. Reproducido: 2 violaciones reales suprimidas (count: 2), arreglamos una, introdujimos otra distinta en el mismo archivo, el count se mantiene en 2 — ni el lint normal ni `--prune-suppressions` (la herramienta de auditoría dedicada) lo detectan. Pedimos que `--prune-suppressions` marque, al menos como warning, buckets donde el count coincide pero el set de ocurrencias cambió.

Qué pasó: este es el issue origen — todos los demás casos de "count sin identidad" (stylelint, oxc, golangci-lint, phpstan, swiftlint) citan este issue como precedente. Sin respuesta de mantenedores de ESLint todavía.

---

## 11 · realm/SwiftLint #6871 — la respuesta más fuerte de toda la sesión

**Link**: https://github.com/realm/SwiftLint/issues/6871
**Estado**: Open. **Confirmado por un colaborador, con PR de fix ya abierta.**

Qué planteamos: el baseline de SwiftLint matchea primero por identidad exacta (rule, file, line, character, reason, texto de línea), y lo que queda se agrupa por rule + (texto de línea + reason), sin ubicación. Reproducido reimplementando el algoritmo desde la fuente: dos violaciones idénticas de `todo` en líneas distintas, arreglamos una, aparece una nueva con texto y reason idénticos en otra línea — el conteo por bucket coincide y la nueva violación desaparece. Afecta reglas con reason genérico y líneas cortas repetidas (todo, mark, orphaned_doc_comment).

Qué pasó — historia real, la más fuerte de los 22 casos:
- **LizunovSergey** reprodujo el bug de forma independiente con un test de regresión propio: "keeping one duplicate violation and replacing the other with a same-text/same-reason violation leaves the filter result empty."
- Identificó el trade-off de diseño correctamente: simplemente agregar ubicación al fallback key atrapa el reemplazo, pero rompe el comportamiento intencional que ignora violaciones uniformemente desplazadas (shifted). Una entrada de baseline consciente del contexto distinguiría más casos, pero necesita una decisión de formato/compatibilidad.
- Abrió **PR #6872** ("Fix baseline duplicate replacements") con el fix, y está pidiendo confirmación de un mantenedor sobre la precedencia correcta antes de avanzar con la implementación.
- Pendiente: que un mantenedor de SwiftLint responda y la PR se mergee.

---

## 12 · golangci/golangci-lint #6714 — CERRADO (declined)

**Link**: https://github.com/golangci/golangci-lint/issues/6714
**Estado**: **Closed** (declined).

Qué planteamos: una regla de exclusión por path (`linters.exclusions.rules`) permite cualquier finding de ese linter bajo ese path, no uno específico. `warn-unused` solo chequea si la regla matcheó algo, no si es lo mismo que matcheaba antes. Reproducido con golangci-lint 2.12.2: arreglamos una violación real de `errcheck`, introdujimos otra distinta bajo el mismo path excluido, `warn-unused` se mantiene silencioso porque la regla "sigue en uso" — aunque cubre algo distinto de lo que la justificó originalmente.

Qué pasó — con historia real:
- **ldez** (mantenedor) marcó como `declined` y cerró, argumentando que "técnicamente el linter se usa igual aunque lo suprimas", señalando el modo verbose (`Skipped 1 issues by rules...`) como evidencia de que "todo está correcto".
- Respondimos señalando que el conteo verbose es idéntico en ambos estados (antes y después del swap) — 1 issue skipped en los dos casos — por lo que no distingue "misma violación" de "violación distinta, misma regla", que es justo lo que `warn-unused` debería atrapar y no atrapa. Cerramos con tono llano, sin pedir reapertura ("Not asking to reopen if that's not useful to you, just wanted to make sure the actual claim landed").
- Sin respuesta posterior. Cerrado, sin reapertura.

---

## 13 · stylelint/stylelint #9438

**Link**: https://github.com/stylelint/stylelint/issues/9438
**Estado**: Open. Sin historia — 0 comentarios.

Qué planteamos: `stylelint-suppressions.json` (Bulk Suppressions, modelado explícitamente sobre el RFC de ESLint, PR #8564) tiene el mismo defecto que #21226: count por `(file, rule)`, no por violación. Verificado con stylelint 17.14.1 empíricamente en `/home/claude/stylelint_test/`. A diferencia de ESLint, stylelint ni siquiera tiene un equivalente a `--prune-suppressions` — no hay ningún camino de auditoría.

Qué pasó: nada aún.

---

## 14 · oxc-project/oxc — comentario en Discussion #22198 (no es issue propio)

**Link**: https://github.com/oxc-project/oxc/discussions/22198
**Estado**: Comentario publicado, sin replies (1 like).

Qué planteamos: revisando el código fuente mergeado (`crates/oxc_linter/src/suppression/mod.rs`, PR #19328), `DiagnosticCounts` es el mismo bucket de count plano que ESLint y stylelint — mismo blindspot. La diferencia: **todavía no está liberado en npm** (1.78.0 no tiene `--suppress-all`), así que sigue siendo cambiable antes del release. Recomendamos construir el audit de prune-time desde el diseño inicial, citando eslint#21226 y stylelint#9438 como precedente ya verificado.

Qué pasó: sin respuesta de los mantenedores de oxc todavía. Es distinto de los demás casos porque llegó antes del release — ventana de oportunidad real si lo leen a tiempo.

---

## 15 · microsoft/agent-governance-toolkit — MSRC (no es GitHub issue, vulnerabilidad viva)

**Estado**: **VULN-211868**, Case number **136137**, status **Review / Repro** ("We're reproducing and evaluating the issue"). Pasó de Submitted a Review/Repro — entró a la cola de evaluación activa, pero sin mensaje humano todavía, solo el cambio de estado automático.

Qué planteamos — el hallazgo más serio de todos, por tratarse de una vulnerabilidad de seguridad viva, no de un gap de diseño: en `agentmesh.governance.policy` (`agent-governance-python/agent-mesh/src/agentmesh/governance/policy.py`), `PolicyRule._eval_expression()` es un mini-parser por regex que solo soporta 6 formas de expresión. Cualquier condición fuera de esa gramática devuelve `False` **silenciosamente** (no excepción). El fail-closed del motor (comentario "V27" en el código) solo dispara ante excepción real, no ante sintaxis no reconocida — así que una regla `deny` con una condición fuera de la gramática soportada nunca dispara, y el motor cae al `default_action` de la policy (`"allow"` en el propio ejemplo del README). Contradice directamente ADR-0013, que declara el motor fail-closed sin flags para fail-open. Reproducido dos veces: standalone y end-to-end (`PolicyEngine.evaluate()` con `decision.allowed == True` sobre una regla deny).

Qué pasó — camino de disclosure, no de issue tracker:
- Se descartó GitHub issue público desde el principio — `SECURITY.md` del repo pide explícitamente no reportar así.
- Intento por email a `secure@microsoft.com` — **rebotó**, MSRC ya no acepta submissions nuevas por email plano.
- Se completó el formulario del MSRC Researcher Portal, incluyendo un párrafo de reclamo explícito sobre la fricción del proceso (el bounce de email) y pedido de un canal de contacto directo para próximas veces, tal como se pidió.
- **Enviado.** Status: Submitted. Security Impact: Security Feature Bypass. Producto: Copilot, AI + ML, and LLMs. Commit de referencia: `7d0cef5d9820a865c3c19b07bd39ecf7053b58a1`.
- Pendiente: respuesta de MSRC Case Management, que según el propio portal llega vía la pestaña Activity del reporte, no por email.

---

## Tabla resumen

| # | Caso | Estado | Historia |
|---|------|--------|----------|
| 1 | ArchUnit #1700 | Open | No |
| 2 | dependency-cruiser #1078 | Open | No |
| 3 | import-linter #375 | Open | No |
| 4 | openrewrite/rewrite #8498 | Open | No |
| 5 | Hypothesis #4854 | **Closed** | Sí — cerrado por política AI, respondimos, sin reapertura |
| 6 | spec-kit #4106 | Open | **Sí — colaboración activa, feedback positivo** |
| 7 | eslint #21223 | Open | **Sí — mantenedor pidió clarificación, respondimos, a la espera** |
| 8 | phpstan #15078 | **Closed (completed)** | Sí — respuesta hostil, respondimos, mantenedor volvió con respuesta técnica sustantiva y cerró |
| 9 | rubocop #15570 | Open | No |
| 10 | eslint #21226 | Open | No (es el issue-origen citado por los demás) |
| 11 | SwiftLint #6871 | Open | **Sí — confirmado + PR de fix (#6872) por un colaborador** |
| 12 | golangci-lint #6714 | **Closed (declined)** | Sí — declined, respondimos, sin reapertura |
| 13 | stylelint #9438 | Open | No |
| 14 | oxc discussion #22198 | Comentario publicado | No (1 like, sin reply) |
| 15 | MS Agent Governance Toolkit (MSRC) | **Review/Repro**, case 136137 | En cola activa, sin mensaje humano aún |
| 16 | bandit #1467 | Open | No (recién posteado) |
| 17 | checkov #7647 | Open | No (recién posteado) |
| 18 | diff_cover #619 | **Closed (completed)** | No — cerrado sin comentario ni PR/commit vinculado |
| 19 | pa11y-ci #352 | Open | No (recién posteado) |
| 20 | lighthouse-ci #1145 | Open | No (recién posteado) |
| 21 | eslint-seatbelt #30 | Open | No (recién posteado) |
| 22 | codecov/umbrella #1989 | Open | No (recién posteado) |
| 23 | gitleaks #2239 | Open | No (recién posteado) |
| — | mypy-baseline | **Bloqueado, sin issue** | Vulnerable confirmado en vivo, repo no acepta issues públicas |
| 24 | vulture #430 | Open | No (recién posteado) |
| 25 | knip #1949 | Open | No (recién posteado) |
| 26 | deptry #1654 | **Closed (not planned)** | Sí — "AI slop", respondimos con tono neutro, sin reapertura |
| 27 | jscpd #938 | Open | **Sí — mantenedor aceptó el planteo, diseño de baseline publicado** |
| 28 | simian #23 | Open | No (recién posteado) |
| 29 | audit-ci #356 | Open | No (recién posteado) |
| 30 | pip-audit #1113 | **Closed (not planned)** | No — cerrado sin comentario |
| 31 | safety #907 | Open | No (recién posteado) |
| 32 | maven-pmd-plugin #724 | Open | No (recién posteado, code-only) |
| 33 | dependency-check #8751 | **Closed (not planned)** | No — cerrado sin comentario |
