# Casos — defectos estructurales reportados

Corte: 2026-08-15/16 (actualizado tras el segundo rastrillaje de 6 categorías nuevas y el filing de 11 issues más — 9 en vivo + 2 code-only). Ledger de todos los casos donde participé: qué se planteó, qué aporté, qué pasó, si hay historia o no. Verificado contra el estado real de cada issue/discussion/reporte, no contra memoria de sesión.

Resumen: 31 issues de GitHub + 1 comentario en discussion + 1 reporte MSRC = 33 casos (+ 1 intento bloqueado, mypy-baseline, no cuenta como caso). 3 cerrados (1 declined, 1 closed-por-política-de-AI-sin-disclosure, 1 closed-completed tras respuesta sustantiva del mantenedor). 4 con intercambio real con mantenedor o colaborador — el más fuerte, SwiftLint #6871, ya tiene PR de fix abierta por un tercero; el más reciente, phpstan #15078, pasó de respuesta hostil a respuesta técnica y cierre. MSRC en Review/Repro (cola activa, sin mensaje humano aún). El resto, sin respuesta todavía (esperable en los 18 más nuevos, recién posteados).

---

## 1 · TNG/ArchUnit #1700

**Link**: https://github.com/TNG/ArchUnit/issues/1700
**Estado**: Open. Sin historia — 0 comentarios.

Qué planteé: `FreezingArchRule` está bien diseñado (absorbe violación puntual, no permite la clase — no degenera como el criterio de separación genérico predice). El gap: no expone el tamaño del store congelado ni su delta. Un build verde con el store creciendo 40 líneas y uno con el store bajando 40 son indistinguibles desde afuera. Pedí exponer store size / delta.

Qué pasó: nada aún.

---

## 2 · sverweij/dependency-cruiser #1078

**Link**: https://github.com/sverweij/dependency-cruiser/issues/1078
**Estado**: Open. Sin historia — 0 comentarios.

Qué planteé: el known-violations file hace bien la granularidad (por instancia, no por regla), y ya imprime el count en cada corrida verde — mejor que la mayoría. Pero el count es nivel, no cambio: `⚠ 20 known violations ignored` es igual si el store creció de 12 a 20 o bajó de 28 a 20. Pedí delta contra el archivo en disco, y/o modo shrink-only para `depcruise-baseline`.

Qué pasó: nada aún.

---

## 3 · seddonym/import-linter #375

**Link**: https://github.com/seddonym/import-linter/issues/375
**Estado**: Open. Sin historia — 0 comentarios.

Qué planteé: `ignore_imports` con entradas literales está bien (absorción por instancia) y `unmatched_ignore_imports_alerting` (default error) fuerza a limpiar excepciones muertas — ambos correctos. El gap: los wildcards (`**`) rompen ambas garantías a la vez, permiten una clase entera y quedan "matched" mientras el conjunto que cubren crece de 1 a 40 sin que nada alerte. Pedí trackear match count por expresión wildcard y alertar cuando crece.

Qué pasó: nada aún.

---

## 4 · openrewrite/rewrite #8498

**Link**: https://github.com/openrewrite/rewrite/issues/8498
**Estado**: Open. Sin historia — 0 comentarios de mantenedores (solo bot de project automation agregándolo al board).

Qué planteé: `runRecipeCycles` sale por tres caminos (fixpoint, panic, budget agotado) y los tres devuelven el mismo `RecipeRun`, sin distinguir cuál fue. Argumenté con mi propio trabajo en reparación estructural iterada: la salida por budget agotado no es un resultado, es un artefacto de control — y cité un issue previo del propio repo (#2415) cerrado con una respuesta que trata ambas salidas como equivalentes. Pedí exponer cycle count y/o razón de terminación.

Qué pasó: nada aún, más allá del bot de triage.

---

## 5 · HypothesisWorks/hypothesis #4854 — CERRADO

**Link**: https://github.com/HypothesisWorks/hypothesis/issues/4854
**Estado**: **Closed** (por el mantenedor).

Qué planteé: sugerencia de documentación — un property test que lee su umbral desde el módulo bajo test es tautológico (no puede fallar aunque el umbral esté mal), y property-based testing empuja exactamente hacia esa forma por buenas prácticas (no duplicar el literal). Con medición propia: 17 de 19 reglas de una suite siguieron en verde con una constante rota a propósito. Propuse la regla del "un ancla": un solo test que fija el valor literal, el resto referencia la constante real.

Qué pasó — con historia real:
— **Liam-DeVoe** (mantenedor) cerró el issue citando la política de uso de AI del repo (CONTRIBUTING.rst#ai-policy) — el issue no tenía el disclosure requerido de asistencia por IA.

— Respondí reconociendo el error explícitamente ("Fair, and my mistake"), aclaré los términos que la política pide (dirección de la investigación, verificación de fuentes, responsabilidad del contenido), notifiqué que agregué disclosure a los demás issues abiertos, y ofrecí reescribirlo más corto y en palabras propias si el maintainer lo prefería.

— Sin respuesta posterior. Issue permanece cerrado, sin reapertura.

---

## 6 · github/spec-kit #4106 — el caso con más interacción

**Link**: https://github.com/github/spec-kit/issues/4106
**Estado**: Open. Colaboración activa, en curso.

Qué planteé: `/speckit.analyze` no detecta cuando un vocabulario cerrado (un set de valores admisibles) se enumera distinto en distintas partes de una spec — ni la verificación estructural ni el oráculo de trayectorias lo cubren. Con un caso medido propio: un sistema de 40 módulos generado por contrato, 4 defectos sobrevivieron verificación completa, los 4 eran la misma clase de error (transcripción de una definición), uno de ellos exactamente este.

Qué pasó — historia sustancial:
— **mnriem** (colaborador) respondió pidiendo que se construya primero como preset para ver cómo funciona en la práctica.

— Acepté, pregunté sobre la forma correcta de extender un comando core sin tener que mantener una copia completa. mnriem aclaró: los presets soportan append/prepend/wrap, no solo replace.

— Publiqué una **corrección propia** sobre el caso motivador original (el párrafo que describía el defecto estaba mal — las dos enumeraciones del contrato entregado en realidad coincidían, la que difería era un estado intermedio de la corrida, no el artefacto final) — con errata publicada en Zenodo (DOI 10.5281/zenodo.21926137), y honestamente señalé qué le cuesta esto al pedido original (el check propuesto no habría atrapado ese caso específico).

— Aporté un caso real distinto encontrado con un checker propio corriendo sobre 6 contratos propios (553 declaraciones), con una divergencia real de 4 vs 6 miembros del mismo set.

— Construí el preset (append, no copy), con 3 fixtures (1 positivo, 2 controles negativos), reporte de coverage obligatorio, y honestidad explícita sobre 3 corridas del check contra sus propias fixtures donde 2 de 3 dieron resultado correcto por razón equivocada (grouping fallaba silenciosamente).

— **mnriem**: "Solid — appreciate the correction, and the 4-vs-6 case is squarely what this is for. Looking forward to the preset." — feedback positivo explícito.

— Reporté medición de campo sobre 11 contratos reales propios: 5 no aplican, 6 con 303 declaraciones inspeccionadas, 44 reconocidas (15%), 0 divergencias — con la limitación declarada de que sé que hay una divergencia real en ese corpus que el check no puede alcanzar (es contra la fuente, no entre declaraciones).

— Issue sigue abierto. Pendiente: correr `/speckit-analyze` end-to-end con un agente real (declarado explícitamente como no hecho todavía).

---

## 7 · eslint/eslint #21223

**Link**: https://github.com/eslint/eslint/issues/21223
**Estado**: Open. Sin historia — solo bot de triage (movido a "Needs Triage").

Qué planteé: distinguí este pedido de dos issues previos relacionados y cerrados (#19321 cerrado como duplicado de #17609; #17609 resuelto por detección de fixes circulares en v9.23.0) — argumenté que la detección circular no cubre el caso real: salida silenciosa por `MAX_AUTOFIX_PASSES` agotado con fixes pendientes. Mismo argumento de fondo que en openrewrite: presupuesto agotado no es un resultado, es un control.

Qué pasó: nada aún, más allá del triage automático.

---

## 8 · phpstan/phpstan #15078 — CERRADO, de la respuesta más hostil a cierre técnico

**Link**: https://github.com/phpstan/phpstan/issues/15078
**Estado**: **Closed** (completed, por el mantenedor).

Qué planteé: `ignoreErrors` de PHPStan es más fuerte que la mayoría de baselines comparables — bounded en ambas direcciones (más ocurrencias de las registradas, falla; menos, también falla con `reportUnmatchedIgnoredErrors`). El gap está adentro de un mismo bucket `(path, message-regex) → count`: reproducido, arreglé una violación real e introduje otra distinta del mismo tipo en el mismo archivo, el count se mantuvo en 2, `[OK] No errors`.

Qué pasó — con historia real, verificado en vivo el 2026-08-15:
— **ondrejmirtes** (mantenedor) respondió primero: *"I'm sorry, this is unreadable AI slop. Please tell me in plain words what are you asking."*

— Respondí en términos llanos, reformulando la afirmación en una frase, y señalé directamente que el tono no correspondía a cómo se abre una respuesta a un reporte, sin escalar ni disculparme de más.

— **ondrejmirtes** volvió con una respuesta sustantiva: *"The current granularity is a good compromise which works pretty well. Finer granularity would require tracking exact lines and recompute them based on Git diffs. There are projects that do that, like https://github.com/DaveLiddament/sarb."* — reconoce el gap, lo justifica como trade-off deliberado, y señala una herramienta externa (sarb) que sí trackea por línea vía diffs de Git.

— Cerrado como completed. Sin reapertura.

---

## 9 · rubocop/rubocop #15570

**Link**: https://github.com/rubocop/rubocop/issues/15570
**Estado**: Open. Sin historia — 0 comentarios.

Qué planteé: `TodoAudit#offending_cops_without_todo` filtra por `File.file?`, que es `false` para un string con glob — así que cualquier entrada `Exclude` con wildcard (`**/*_spec.rb`, típico en cops de RSpec generadas automáticamente) nunca entra al set auditado, nunca se marca como stale. Verificado con búsqueda de código: ~6.300 repos con este patrón, incluyendo proyectos grandes (mongodb/mongoid, gocardless/statesman). Pedí expandir los globs antes del filtro.

Qué pasó: nada aún.

---

## 10 · eslint/eslint #21226 — el caso original de la línea de trabajo

**Link**: https://github.com/eslint/eslint/issues/21226
**Estado**: Open. Sin respuesta directa de mantenedores — solo etiquetado automático (`core`, `enhancement`) y bot de triage.

Qué planteé: `eslint-suppressions.json` bucketiza por `(file, rule) → count`, no por identidad de violación. Reproducido: 2 violaciones reales suprimidas (count: 2), arreglé una, introduje otra distinta en el mismo archivo, el count se mantiene en 2 — ni el lint normal ni `--prune-suppressions` (la herramienta de auditoría dedicada) lo detectan. Pedí que `--prune-suppressions` marque, al menos como warning, buckets donde el count coincide pero el set de ocurrencias cambió.

Qué pasó: este es el issue origen — todos los demás casos de "count sin identidad" (stylelint, oxc, golangci-lint, phpstan, swiftlint) citan este issue como precedente. Sin respuesta de mantenedores de ESLint todavía.

---

## 11 · realm/SwiftLint #6871 — la respuesta más fuerte de toda la sesión

**Link**: https://github.com/realm/SwiftLint/issues/6871
**Estado**: Open. **Confirmado por un colaborador, con PR de fix ya abierta.**

Qué planteé: el baseline de SwiftLint matchea primero por identidad exacta (rule, file, line, character, reason, texto de línea), y lo que queda se agrupa por rule + (texto de línea + reason), sin ubicación. Reproducido reimplementando el algoritmo desde la fuente: dos violaciones idénticas de `todo` en líneas distintas, arreglé una, aparece una nueva con texto y reason idénticos en otra línea — el conteo por bucket coincide y la nueva violación desaparece. Afecta reglas con reason genérico y líneas cortas repetidas (todo, mark, orphaned_doc_comment).

Qué pasó — historia real, la más fuerte de los casos:
— **LizunovSergey** reprodujo el bug de forma independiente con un test de regresión propio: "keeping one duplicate violation and replacing the other with a same-text/same-reason violation leaves the filter result empty."

— Identificó el trade-off de diseño correctamente: simplemente agregar ubicación al fallback key atrapa el reemplazo, pero rompe el comportamiento intencional que ignora violaciones uniformemente desplazadas (shifted). Una entrada de baseline consciente del contexto distinguiría más casos, pero necesita una decisión de formato/compatibilidad.

— Abrió **PR #6872** ("Fix baseline duplicate replacements") con el fix, y está pidiendo confirmación de un mantenedor sobre la precedencia correcta antes de avanzar con la implementación.

— Pendiente: que un mantenedor de SwiftLint responda y la PR se mergee.

---

## 12 · golangci/golangci-lint #6714 — CERRADO (declined)

**Link**: https://github.com/golangci/golangci-lint/issues/6714
**Estado**: **Closed** (declined).

Qué planteé: una regla de exclusión por path (`linters.exclusions.rules`) permite cualquier finding de ese linter bajo ese path, no uno específico. `warn-unused` solo chequea si la regla matcheó algo, no si es lo mismo que matcheaba antes. Reproducido con golangci-lint 2.12.2: arreglé una violación real de `errcheck`, introduje otra distinta bajo el mismo path excluido, `warn-unused` se mantiene silencioso porque la regla "sigue en uso" — aunque cubre algo distinto de lo que la justificó originalmente.

Qué pasó — con historia real:
— **ldez** (mantenedor) marcó como `declined` y cerró, argumentando que "técnicamente el linter se usa igual aunque lo suprimas", señalando el modo verbose (`Skipped 1 issues by rules...`) como evidencia de que "todo está correcto".

— Respondí señalando que el conteo verbose es idéntico en ambos estados (antes y después del swap) — 1 issue skipped en los dos casos — por lo que no distingue "misma violación" de "violación distinta, misma regla", que es justo lo que `warn-unused` debería atrapar y no atrapa. Cerré con tono llano, sin pedir reapertura ("Not asking to reopen if that's not useful to you, just wanted to make sure the actual claim landed").

— Sin respuesta posterior. Cerrado, sin reapertura.

---

## 13 · stylelint/stylelint #9438

**Link**: https://github.com/stylelint/stylelint/issues/9438
**Estado**: Open. Sin historia — 0 comentarios.

Qué planteé: `stylelint-suppressions.json` (Bulk Suppressions, modelado explícitamente sobre el RFC de ESLint, PR #8564) tiene el mismo defecto que #21226: count por `(file, rule)`, no por violación. Verificado con stylelint 17.14.1 empíricamente en un entorno de test local. A diferencia de ESLint, stylelint ni siquiera tiene un equivalente a `--prune-suppressions` — no hay ningún camino de auditoría.

Qué pasó: nada aún.

---

## 14 · oxc-project/oxc — comentario en Discussion #22198 (no es issue propio)

**Link**: https://github.com/oxc-project/oxc/discussions/22198
**Estado**: Comentario publicado, sin replies (1 like).

Qué planteé: revisando el código fuente mergeado (`crates/oxc_linter/src/suppression/mod.rs`, PR #19328), `DiagnosticCounts` es el mismo bucket de count plano que ESLint y stylelint — mismo blindspot. La diferencia: **todavía no está liberado en npm** (1.78.0 no tiene `--suppress-all`), así que sigue siendo cambiable antes del release. Recomendé construir el audit de prune-time desde el diseño inicial, citando eslint#21226 y stylelint#9438 como precedente ya verificado.

Qué pasó: sin respuesta de los mantenedores de oxc todavía. Es distinto de los demás casos porque llegó antes del release — ventana de oportunidad real si lo leen a tiempo.

---

## 15 · microsoft/agent-governance-toolkit — MSRC (no es GitHub issue, vulnerabilidad viva)

**Estado**: **VULN-211868**, Case number **136137**, status **Review / Repro** ("We're reproducing and evaluating the issue"). Pasó de Submitted a Review/Repro — entró a la cola de evaluación activa, pero sin mensaje humano todavía, solo el cambio de estado automático.

Qué planteé — el hallazgo más serio de todos, por tratarse de una vulnerabilidad de seguridad viva, no de un gap de diseño: en `agentmesh.governance.policy` (`agent-governance-python/agent-mesh/src/agentmesh/governance/policy.py`), `PolicyRule._eval_expression()` es un mini-parser por regex que solo soporta 6 formas de expresión. Cualquier condición fuera de esa gramática devuelve `False` **silenciosamente** (no excepción). El fail-closed del motor (comentario "V27" en el código) solo dispara ante excepción real, no ante sintaxis no reconocida — así que una regla `deny` con una condición fuera de la gramática soportada nunca dispara, y el motor cae al `default_action` de la policy (`"allow"` en el propio ejemplo del README). Contradice directamente ADR-0013, que declara el motor fail-closed sin flags para fail-open. Reproducido dos veces: standalone y end-to-end (`PolicyEngine.evaluate()` con `decision.allowed == True` sobre una regla deny).

Qué pasó — camino de disclosure, no de issue tracker:
— Descarté GitHub issue público desde el principio — `SECURITY.md` del repo pide explícitamente no reportar así.

— Intento por email a `secure@microsoft.com` — **rebotó**, MSRC ya no acepta submissions nuevas por email plano.

— Completé el formulario del MSRC Researcher Portal, incluyendo un párrafo de reclamo explícito sobre la fricción del proceso (el bounce de email) y pedido de un canal de contacto directo para próximas veces, tal como se pidió.

— **Enviado.** Status: Submitted. Security Impact: Security Feature Bypass. Producto: Copilot, AI + ML, and LLMs. Commit de referencia: `7d0cef5d9820a865c3c19b07bd39ecf7053b58a1`.

— Pendiente: respuesta de MSRC Case Management, que según el propio portal llega vía la pestaña Activity del reporte, no por email.

---

## 16 · PyCQA/bandit #1467

**Link**: https://github.com/PyCQA/bandit/issues/1467
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: baseline por `Issue.__eq__` sin `lineno`, matching por pertenencia no multiplicidad, absorbe N hallazgos sin límite.

Qué pasó: nada aún.

---

## 17 · bridgecrewio/checkov #7647

**Link**: https://github.com/bridgecrewio/checkov/issues/7647
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: baseline matchea por resource+check_id ignorando el archivo, colapso cross-file.

Qué pasó: nada aún.

---

## 18 · Bachmann1234/diff_cover #619

**Link**: https://github.com/Bachmann1234/diff_cover/issues/619
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: `total_percent_covered()` agrega todo el diff, bug real diluido por líneas triviales.

Qué pasó: nada aún.

---

## 19 · pa11y/pa11y-ci #352

**Link**: https://github.com/pa11y/pa11y-ci/issues/352
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: threshold por página entera, todas las reglas mezcladas.

Qué pasó: nada aún.

---

## 20 · GoogleChrome/lighthouse-ci #1145

**Link**: https://github.com/GoogleChrome/lighthouse-ci/issues/1145
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: `maxLength` cuenta items del audit sin comparar selector.

Qué pasó: nada aún.

---

## 21 · justjake/eslint-seatbelt #30

**Link**: https://github.com/justjake/eslint-seatbelt/issues/30
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: solo código (declarado explícitamente en el issue) — hereda el patrón de ESLint bulk suppressions a grano más fino.

Qué pasó: nada aún.

---

## 22 · codecov/umbrella #1989

**Link**: https://github.com/codecov/umbrella/issues/1989
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: solo código (SaaS, declarado explícitamente) — mismo patrón que diff_cover, target "auto" lo hace explotable sin configuración especial.

Qué pasó: nada aún.

---

## 23 · gitleaks/gitleaks #2239

**Link**: https://github.com/gitleaks/gitleaks/issues/2239
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: en vivo — `.gitleaksignore` fingerprint `file:rule:line` sin contenido del secreto.

Qué pasó: nada aún.

---

## — · orsinium-labs/mypy-baseline — bloqueado, sin issue

**Estado**: **Bloqueado, sin issue.** El repo tiene "Issue creation is restricted in this repository" y no tiene Discussions habilitadas — sin canal público para reportarlo.

Qué planteé: vulnerable confirmado con repro en vivo — `preserve_position=False` por default permite swap de mismo mensaje en otra línea del mismo archivo, invisible.

Qué pasó: queda documentado acá sin issue.

---

## 24 · jendrikseipp/vulture #430

**Link**: https://github.com/jendrikseipp/vulture/issues/430
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: whitelist matchea por nombre de símbolo puro, sin archivo/línea.

Qué pasó: nada aún.

---

## 25 · webpro-nl/knip #1949

**Link**: https://github.com/webpro-nl/knip/issues/1949
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: `--max-issues` gatea por count agregado.

Qué pasó: nada aún.

---

## 26 · osprey-oss/deptry (ex fpgmaas/deptry) #1654

**Link**: https://github.com/fpgmaas/deptry/issues/1654
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: `--per-rule-ignores` matchea por nombre de módulo puro (DEP001/003/004).

Qué pasó: nada aún.

---

## 27 · kucherenko/jscpd #938

**Link**: https://github.com/kucherenko/jscpd/issues/938
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: `ThresholdReporter` solo mira % agregado.

Qué pasó: nada aún.

---

## 28 · quandarypeak/simian #23

**Link**: https://github.com/quandarypeak/simian/issues/23
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: `Checker.check()` gatea por count/% agregado sin ubicación.

Qué pasó: nada aún.

---

## 29 · IBM/audit-ci #356

**Link**: https://github.com/IBM/audit-ci/issues/356
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: allowlist por GHSA-ID bare, sin scoping de paquete/versión por default.

Qué pasó: nada aún.

---

## 30 · pypa/pip-audit #1113

**Link**: https://github.com/pypa/pip-audit/issues/1113
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: `--ignore-vuln` global sin scoping de paquete/versión.

Qué pasó: nada aún.

---

## 31 · pyupio/safety #907

**Link**: https://github.com/pyupio/safety/issues/907
**Estado**: Open. Recién posteado, sin respuesta todavía.

Qué planteé: `ignore-vulnerabilities` por ID puro, sin binding a paquete/versión, sin expiry por default.

Qué pasó: nada aún.

---

## 32 · apache/maven-pmd-plugin #724

**Link**: https://github.com/apache/maven-pmd-plugin/issues/724
**Estado**: Open. Recién posteado, sin respuesta todavía. Solo código (declarado explícitamente — bloqueo de red a Maven Central).

Qué planteé: `excludeFromFailureFile` excluye por par-de-archivos completo, `maxAllowedViolations` es count puro.

Qué pasó: nada aún.

---

## 33 · dependency-check/DependencyCheck #8751

**Link**: https://github.com/dependency-check/DependencyCheck/issues/8751
**Estado**: Open. Recién posteado, sin respuesta todavía. Solo código (declarado explícitamente — build+NVD feed inviable).

Qué planteé: regla `<cve>` sin cpe/gav/purl/sha1 matchea cualquier dependencia con ese CVE. Nota: el repo original `jeremylong/DependencyCheck` está archivado desde sep-2025; filed en el fork activo `dependency-check/DependencyCheck`.

Qué pasó: nada aún.

---

## Limpias / no aplican / no fileadas por baja confianza

Verificadas y descartadas en el rastrillaje, sin issue abierto:

**Primer rastrillaje** (24 herramientas en SAST, IaC, coverage gates, test flake quarantine, policy engines, accesibilidad): CodeQL, Snyk, Trivy, Grype, tfsec, KICS, terrascan, nyc, coverage.py, pytest, Jest, Buildkite, CircleCI, axe-core, eslint-plugin-jsx-a11y — limpias o no aplican. OPA/Rego y Cedar limpios por documentación honesta, Kyverno con bugs ya parcheados, Gatekeeper con fail-open documentado por el propio proyecto.

**Segundo rastrillaje** (secret scanning, type-checker baselines, dead-code baselines, duplicate-code detectors, dependency-vuln suppression, quality gates de código nuevo): detect-secrets, git-secrets, trufflehog, pyright, Pyre, typescript-strict-plugin, ts-prune, PMD CPD (núcleo), Code Climate (discontinuado, absorbido por Qlty) — limpias o no aplican. Vulnerables solo-documentación, no fileadas por baja confianza: SonarQube (coverage/duplicación de "new code", no los issues discretos — esos sí trackean identidad), Codacy (Diff Coverage), Qlty (Diff Coverage) — los tres SaaS o motor de gate cerrado.

Metodología del rastrillaje: búsquedas en paralelo por categoría, código fuente real leído en cada caso, repro en vivo priorizado sobre code-only. 9 de 10 candidatos con repro en vivo se llegaron a filear — el décimo, mypy-baseline, documentado arriba sin issue por falta de canal público.

---

## Índice — estado e historia, caso por caso

Caso 1 — ArchUnit #1700 — Open — sin historia

Caso 2 — dependency-cruiser #1078 — Open — sin historia

Caso 3 — import-linter #375 — Open — sin historia

Caso 4 — openrewrite/rewrite #8498 — Open — sin historia

Caso 5 — Hypothesis #4854 — **Closed** — cerrado por política AI, respondí, sin reapertura

Caso 6 — spec-kit #4106 — Open — **colaboración activa, feedback positivo**

Caso 7 — eslint #21223 — Open — sin historia

Caso 8 — phpstan #15078 — **Closed (completed)** — respuesta hostil, respondí, mantenedor volvió con respuesta técnica sustantiva y cerró

Caso 9 — rubocop #15570 — Open — sin historia

Caso 10 — eslint #21226 — Open — sin historia (es el issue-origen citado por los demás)

Caso 11 — SwiftLint #6871 — Open — **confirmado + PR de fix (#6872) por un colaborador**

Caso 12 — golangci-lint #6714 — **Closed (declined)** — declined, respondí, sin reapertura

Caso 13 — stylelint #9438 — Open — sin historia

Caso 14 — oxc discussion #22198 — comentario publicado — sin historia (1 like, sin reply)

Caso 15 — MS Agent Governance Toolkit (MSRC) — **Review/Repro**, case 136137 — en cola activa, sin mensaje humano aún

Caso 16 — bandit #1467 — Open — sin historia

Caso 17 — checkov #7647 — Open — sin historia

Caso 18 — diff_cover #619 — Open — sin historia

Caso 19 — pa11y-ci #352 — Open — sin historia

Caso 20 — lighthouse-ci #1145 — Open — sin historia

Caso 21 — eslint-seatbelt #30 — Open — sin historia

Caso 22 — codecov/umbrella #1989 — Open — sin historia

Caso 23 — gitleaks #2239 — Open — sin historia

— mypy-baseline — **bloqueado, sin issue** — vulnerable confirmado en vivo, repo no acepta issues públicas

Caso 24 — vulture #430 — Open — sin historia

Caso 25 — knip #1949 — Open — sin historia

Caso 26 — deptry #1654 — Open — sin historia

Caso 27 — jscpd #938 — Open — sin historia

Caso 28 — simian #23 — Open — sin historia

Caso 29 — audit-ci #356 — Open — sin historia

Caso 30 — pip-audit #1113 — Open — sin historia

Caso 31 — safety #907 — Open — sin historia

Caso 32 — maven-pmd-plugin #724 — Open — sin historia (code-only)

Caso 33 — dependency-check #8751 — Open — sin historia (code-only)
