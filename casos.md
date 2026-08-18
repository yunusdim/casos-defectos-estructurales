# Casos — defectos estructurales reportados

Corte: 2026-08-18, tras auditoría en vivo de los 33 casos. La actualización previa fue del 17-ago (incorpora el movimiento del 16-ago en 6 casos — 4 cierres nuevos y 2 respuestas sustantivas de mantenedor — sobre la base del segundo rastrillaje de 6 categorías nuevas y el filing de 11 issues más: 9 en vivo + 2 code-only). Ledger de todos los casos donde participé: qué se planteó, qué aporté, qué pasó, si hay historia o no. Verificado contra el estado real de cada issue/discussion/reporte, no contra memoria de sesión.

Resumen: 31 issues de GitHub + 1 comentario en discussion + 1 reporte MSRC = 33 casos (+ 1 intento bloqueado, mypy-baseline, no cuenta como caso). 7 cerrados: 3 con comentario del mantenedor (phpstan #15078 completed tras respuesta sustantiva, golangci-lint #6714 declined, deptry #1654 not-planned con "AI slop") y 4 sin ningún comentario (dependency-check #8751, pip-audit #1113 y diff_cover #619, todos not-planned/completed mudos, más Hypothesis #4854 cerrado por política de AI). 7 con intercambio real con mantenedor o colaborador — el más fuerte, SwiftLint #6871, ya tiene PR de fix abierta por un tercero; jscpd #938 tiene un diseño completo de baseline aceptado por el mantenedor (kucherenko); eslint #21223 quedó etiquetado `core` y `feature` tras responder al mantenedor (mdjermanovic), con un colaborador ofreciéndose a implementarlo; stylelint #9438 fue retitulado por el mantenedor (jeddy3) y aceptado como pedido de función. MSRC: caso abierto, bajo reserva por pedido de MSRC. El resto, sin respuesta todavía.

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
**Estado**: Open. **Respuesta sustantiva del owner: descarta (a), acepta (b) y ofrece PR.**

Qué planteé: `ignore_imports` con entradas literales está bien (absorción por instancia) y `unmatched_ignore_imports_alerting` (default error) fuerza a limpiar excepciones muertas — ambos correctos. El gap: los wildcards (`**`) rompen ambas garantías a la vez, permiten una clase entera y quedan "matched" mientras el conjunto que cubren crece de 1 a 40 sin que nada alerte. Pedí (a) trackear match count por expresión wildcard y alertar cuando crece, y (b) reportar el total de imports ignorados en un contrato KEPT.

Qué pasó — verificado en vivo el 17-ago-2026:

— **seddonym** (owner) respondió reformulando el planteo en sus términos: *"the use of wildcards in ignore_imports can mask new violations being added"*, y lo defendió como comportamiento intencional: *"Arguably, that's the point of expressions with wildcards in them... If you want the contract to fail if a new violation is added, just don't use wildcards - list each import individually."*

— Descartó **(a)** con una razón técnica concreta: *"I think I'm going to rule this one out for the moment. At the moment the linter is stateless, that would be a big change."*

— Aceptó **(b)**: *"I'm certainly open to this."* Propuso formato concreto (`Territory layers KEPT (3 ignored imports)`) tras un flag de CLI, con la reserva de que por sí solo quizás no alcance para detectar aumentos, y cerró con *"Happy to consider a PR for it."*

— Adelantó que la UI interactiva futura sería el lugar para el detalle de qué imports matchean cada ignore.

— Respondí el 17-ago-2026: concedí el punto del wildcard, acepté el descarte de (a) por el argumento del linter stateless, y **tomé la PR de (b)**. Adopté el formato que él mismo propuso, respondí su reserva de que el conteo por sí solo no deja ver los aumentos (correcto dentro de una corrida, pero el número queda en el log del build y el aumento aparece en un diff de CI entre corridas), y dejé tres definiciones a cerrar antes de escribir la PR: nombre y default del flag (`--show-ignored-counts`, off por default), caso cero (omitir el paréntesis o imprimir `(0 ignored imports)`), y semántica del conteo (aristas del grafo ignoradas vs entradas de `ignore_imports` que matchearon — para un wildcard que cubre 12 aristas dan 12 y 1; propuse aristas).

— Pendiente: respuesta de seddonym a esas tres definiciones. Sin eso no se puede escribir la PR.

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
**Estado**: Open. Colaboración activa. Preset publicado y submiteado al catálogo. Hay además una PR de un tercero sobre este issue, con cambios pedidos por el mantenedor.

Qué planteé: `/speckit.analyze` no detecta cuando un vocabulario cerrado (un set de valores admisibles) se enumera distinto en distintas partes de una spec — ni la verificación estructural ni el oráculo de trayectorias lo cubren. Con un caso medido propio: un sistema de 40 módulos generado por contrato, 4 defectos sobrevivieron verificación completa, los 4 eran la misma clase de error (transcripción de una definición), uno de ellos exactamente este.

Qué pasó — historia sustancial:
— **mnriem** (colaborador) respondió pidiendo que se construya primero como preset para ver cómo funciona en la práctica.

— Acepté, pregunté sobre la forma correcta de extender un comando core sin tener que mantener una copia completa. mnriem aclaró: los presets soportan append/prepend/wrap, no solo replace.

— Publiqué una **corrección propia** sobre el caso motivador original (el párrafo que describía el defecto estaba mal — las dos enumeraciones del contrato entregado en realidad coincidían, la que difería era un estado intermedio de la corrida, no el artefacto final) — con errata publicada en Zenodo (DOI 10.5281/zenodo.21926137), y honestamente señalé qué le cuesta esto al pedido original (el check propuesto no habría atrapado ese caso específico).

— Aporté un caso real distinto encontrado con un checker propio corriendo sobre 6 contratos propios (553 declaraciones), con una divergencia real de 4 vs 6 miembros del mismo set.

— Construí el preset (append, no copy), con 3 fixtures (1 positivo, 2 controles negativos), reporte de coverage obligatorio, y honestidad explícita sobre 3 corridas del check contra sus propias fixtures donde 2 de 3 dieron resultado correcto por razón equivocada (grouping fallaba silenciosamente).

— **mnriem**: "Solid — appreciate the correction, and the 4-vs-6 case is squarely what this is for. Looking forward to the preset." — feedback positivo explícito.

— Reporté medición de campo sobre 11 contratos reales propios: 5 no aplican, 6 con 303 declaraciones inspeccionadas, 44 reconocidas (15%), 0 divergencias — con la limitación declarada de que sé que hay una divergencia real en ese corpus que el check no puede alcanzar (es contra la fuente, no entre declaraciones).

— **mnriem**, 17-ago-2026: *"Looks like solid progress. Have you rolled it into a preset we can include in the community catalog? Did I miss that?"* — pide el preset explícitamente y menciona inclusión en el community catalog.

— **Un tercero abrió una PR sobre este issue: `github/spec-kit#4160`**, "feat(analyze): detect closed vocabulary drift", por **AnkitPorwal04**, desde `AnkitPorwal04:feat/4106-closed-vocabulary-analysis`, declarando *"Fixes #4106"*. No tengo relación con esa PR ni forkeé el repo. Su propio disclosure declara que fue implementada, testeada y redactada de forma autónoma por OpenAI Codex (GPT-5), y que el operador humano eligió el objetivo pero no revisó el patch línea por línea antes de mandarlo. Mete el pass directo en el core bundled (`templates/commands/analyze.md` más un test), 2 archivos, 1 commit, checks en verde.

— **Estado de esa PR: mnriem pidió cambios** ("Please address Copilot feedback"). El review automático marcó, entre otras cosas, dos puntos que tocan directamente lo que planteé: (1) que meter el pass en el core saltea el rollout acordado en este issue, que era preset primero y después catálogo; y (2) que reportar solo el mismatch count sin cobertura de reconocimiento deja un cero indistinguible de un pass que no reconoció nada — el mismo argumento de count-sin-identidad del issue origen, encontrado de forma independiente.

— Respondí el 17-ago-2026 abriendo con "Not yet", con la evidencia de composición (383 líneas materializadas, core intacto en la línea 161, pass agregado en la 269), la verificación mecánica de las reglas contra las tres fixtures, los tres archivos que faltaban, y el plan de submission. Declaré también que #4160 no es mía y que no estuve involucrado, y aporté dos hallazgos que le pegan: el punto de cobertura es el mismo que el review automático ya había levantado, y el problema de unidad de `declarations inspected` aplica a cualquier implementación en prosa de este pass, incluida esa.

— **Preset publicado el 17-ago-2026**: `github.com/yunusdim/spec-kit-preset-closed-vocabulary`, MIT, 14 archivos, tag `v1.0.0`. Las dos correcciones que había comprometido quedaron aplicadas antes de tagear: la unidad de `declarations inspected` fijada en líneas no vacías, y la regla de relación declarada explicitada como evaluada antes de agrupar, con el caso `shipment status` / `order status` como ejemplo de que la estabilidad es por regla y no por suerte.

— **Entrada submiteada al catálogo**: PR `github/spec-kit#4179`, desde `yunusdim:patch-1`, con la entrada ordenada alfabéticamente entre `claude-ask-questions` y `command-density`. `download_url` coincide carácter por carácter con el comando de instalación del README. No pedí el label `preset-submission` — lo aplica un mantenedor en triage. Faltaba además la fila en la tabla de `docs/community/presets.md` que exige `PUBLISHING.md`; quedó agregada en la misma rama.

— **Corrección, 18-ago-2026.** La versión anterior de esta entrada, y el propio cuerpo del PR #4179, afirmaban que la corrida end-to-end se había hecho con un modelo leyendo la prosa compuesta y que los tres resultados coincidían con la corrida mecánica. Eso no había ocurrido: lo verificado era mecánico, regla por regla, contra las tres fixtures. Peor, el `evidence/RESULTS.md` que viajaba dentro del tag `v1.0.0` declaraba la tabla de resultados como pendiente, contradiciendo la afirmación desde el propio release. Lo levantó el review automático de Copilot en el PR. Corregido en el cuerpo del PR, en el comentario de este issue, y acá.

— **Release `v1.0.1`, 18-ago-2026**: `evidence/RESULTS.md` con la tabla completada desde corridas reales — `001-divergent` dispara, `002-agreeing` y `003-declared-subset` quedan en silencio, cada una con su cobertura declarada. El catálogo y la fila de la documentación apuntan a ese tag.

— **Issue de submission `github/spec-kit#4192`, 18-ago-2026**: mnriem pidió abrirlo para que corran los workflows automáticos del catálogo — la validación se dispara sobre el issue, no sobre el PR. Creado con la plantilla oficial. Label `enhancement`, sin `preset-submission` todavía.

— Issue sigue abierto. Último comentario de mnriem: *"Nice progress!"*. Pendiente, y todo de terceros: aprobación de los workflows del PR #4179 y cierre de CodeQL, más el label `preset-submission` sobre #4192 en triage.

---

## 7 · eslint/eslint #21223

**Link**: https://github.com/eslint/eslint/issues/21223
**Estado**: Open, labels `core` y `feature`, en triage. Con historia — clarificación del mantenedor respondida, y un colaborador ofreciéndose a implementarlo.

Qué planteé: distinguí este pedido de dos issues previos relacionados y cerrados (#19321 cerrado como duplicado de #17609; #17609 resuelto por detección de fixes circulares en v9.23.0) — argumenté que la detección circular no cubre el caso real: salida silenciosa por `MAX_AUTOFIX_PASSES` agotado con fixes pendientes. Mismo argumento de fondo que en openrewrite: presupuesto agotado no es un resultado, es un control.

Qué pasó — verificado en vivo el 16-ago-2026:

— **mdjermanovic** (mantenedor) respondió citando mi propia línea del pedido y preguntando: *"Can you please clarify the request? Is it to emit a warning (`process.emitWarning()`) when fixable errors remain after 10 fix passes for a file?"*

— Confirmé: *"Yes, exactly that — emit a warning (or equivalent) when the loop exits at MAX_AUTOFIX_PASSES with fixable errors still remaining, distinct from the existing circular-fix warning."*

— **18-ago-2026**: el issue quedó etiquetado `core` y `feature`, y entró al proyecto Triage. **solp721** revisó `verifyAndFix` y confirmó el diagnóstico desde el código — la salida por límite de pasadas cae del loop sin registrar nada, mientras el caso circular ya pasa por `WarningService` — y cerró ofreciéndose: *"if you aren't planning to implement this yourself, I'd like to give it a shot once it's accepted."*

— Pendiente, y es mío: decidir si lo implemento o le paso la posta.

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

— Le respondí en la PR el 17-ago-2026: validé el mecanismo de ancla —una violación duplicada sin cambios es la evidencia de que el grupo no se desplazó, que es justo la distinción que el fallback viejo no podía hacer— y declaré el residuo real: si todas las duplicadas de un grupo se reemplazan en la misma corrida, no sobrevive ancla, el grupo cae al camino tolerante al desplazamiento y el swap sigue invisible. Es más angosto que lo reportado en el issue y está acotado por lo genérico que sean reason y texto de línea.

— Pendiente: que un mantenedor apruebe los workflows pendientes y decida sobre la precedencia.

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
**Estado**: Open, retitulado y etiquetado por el mantenedor. Con historia.

Qué planteé: `stylelint-suppressions.json` (Bulk Suppressions, modelado explícitamente sobre el RFC de ESLint, PR #8564) tiene el mismo defecto que #21226: count por `(file, rule)`, no por violación. Verificado con stylelint 17.14.1 empíricamente en un entorno de test local. A diferencia de ESLint, stylelint ni siquiera tiene un equivalente a `--prune-suppressions` — no hay ningún camino de auditoría.

Qué pasó — verificado en vivo el 18-ago-2026: **jeddy3** (mantenedor) retituló el issue, de *"Bulk suppressions bucket by (file, rule) count, not identity: a same-count swap is invisible"* a *"Add support for auditing to bulk suppressions"*, le puso `status: needs discussion` y `triage needs further discussion`, y respondió: *"Thanks for the report. Let's wait to see what ESLint does."* El retítulo es la aceptación: pasó de reporte de bug a pedido de función. Queda enganchado a eslint #21226, que es el issue origen.

---

## 14 · oxc-project/oxc — comentario en Discussion #22198 (no es issue propio)

**Link**: https://github.com/oxc-project/oxc/discussions/22198
**Estado**: Comentario publicado, sin replies (1 like).

Qué planteé: revisando el código fuente mergeado (`crates/oxc_linter/src/suppression/mod.rs`, PR #19328), `DiagnosticCounts` es el mismo bucket de count plano que ESLint y stylelint — mismo blindspot. La diferencia: **todavía no está liberado en npm** (1.78.0 no tiene `--suppress-all`), así que sigue siendo cambiable antes del release. Recomendé construir el audit de prune-time desde el diseño inicial, citando eslint#21226 y stylelint#9438 como precedente ya verificado.

Qué pasó: sin respuesta de los mantenedores de oxc todavía. Es distinto de los demás casos porque llegó antes del release — ventana de oportunidad real si lo leen a tiempo.

---

## 15 · Reporte de seguridad — MSRC (caso abierto, bajo reserva)

**Estado**: Caso abierto en revisión activa con Microsoft Security Response Center (MSRC).

Qué pasó: reporté una vulnerabilidad de seguridad de un proyecto de terceros a través del MSRC Researcher Portal, no por GitHub issue, siguiendo la política de seguridad del propio proyecto. El caso quedó abierto y en cola de evaluación activa. Por pedido explícito de MSRC, los detalles del hallazgo (proyecto, mecanismo, reproducción) quedan reservados mientras dure la investigación y el período de disclosure coordinado — mínimo dos semanas desde la apertura del caso antes de cualquier publicación propia. Pendiente: que MSRC concluya la evaluación y se cumpla ese período de reserva.

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
**Estado**: **Closed (completed)**.

Qué planteé: `total_percent_covered()` agrega todo el diff, bug real diluido por líneas triviales.

Qué pasó: cerrado por **Bachmann1234** sin comentario ni PR/commit vinculado — no queda claro si lo consideró resuelto o simplemente lo cerró. Sin reapertura.

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
**Estado**: **Closed (not planned)**.

Qué planteé: `--per-rule-ignores` matchea por nombre de módulo puro (DEP001/003/004).

Qué pasó — con historia real:

— **mkniewallner** cerró con: *"Yeah this is AI slop, closing."*

— Respondí con tono neutro, señalando que el repro y el gap siguen ahí independientemente del registro usado para plantearlo.

— Sin respuesta posterior. Cerrado, sin reapertura.

---

## 27 · kucherenko/jscpd #938

**Link**: https://github.com/kucherenko/jscpd/issues/938
**Estado**: Open. **Mantenedor aceptó el planteo, diseño de baseline publicado.**

Qué planteé: `ThresholdReporter` solo mira % agregado.

Qué pasó: **kucherenko** (mantenedor) aceptó el planteo — publicó un diseño completo de modo baseline opt-in (`.jscpd-baseline.json`, fingerprint por hash de contenido del fragmento duplicado más rutas, no por número de línea; los clones ausentes del baseline hacen fallar la corrida aunque el total quede bajo `--threshold`; los removidos no molestan; y un flag regenera el baseline). A implementar en el motor Rust v5.

Le respondí el 17-ago-2026 confirmando que las dos decisiones que importan son correctas, y levanté dos puntos. **Multiplicidad del fingerprint**: dos instancias distintas del mismo par de archivos con contenido duplicado idéntico producen el mismo fingerprint, así que quitar una e introducir otra igual en el mismo par deja el conjunto sin cambios — el mismo defecto de este issue un nivel más abajo, un conjunto que registra presencia pero no multiplicidad; se cierra con un count por fingerprint o un índice de ocurrencia. **Regeneración no silenciosa**: el flag de regeneración es el mecanismo de absorción, y es ahí donde este tipo de gate degrada; imprimir qué se agregó y qué se quitó, con números, hace visible el crecimiento sin que la herramienta tenga que recordar nada entre corridas.

Pendiente: respuesta del owner a esos dos puntos, e implementación.

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
**Estado**: **Closed (not planned)**.

Qué planteé: `--ignore-vuln` global sin scoping de paquete/versión.

Qué pasó: cerrado por **William Woodruff** sin ningún comentario. Sin reapertura.

---

## 31 · pyupio/safety #907

**Link**: https://github.com/pyupio/safety/issues/907
**Estado**: Open. Sin respuesta humana — 1 comentario, bot de bienvenida de github-actions (15-ago-2026).

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
**Estado**: **Closed (not planned)**. Solo código (declarado explícitamente — build+NVD feed inviable).

Qué planteé: regla `<cve>` sin cpe/gav/purl/sha1 matchea cualquier dependencia con ese CVE. Nota: el repo original `jeremylong/DependencyCheck` está archivado desde sep-2025; filed en el fork activo `dependency-check/DependencyCheck`.

Qué pasó: cerrado por **chadlwilson** sin ningún comentario. Sin reapertura.

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

Caso 3 — import-linter #375 — Open — **owner aceptó (b) y ofreció PR; la tomé, esperando sus tres definiciones**

Caso 4 — openrewrite/rewrite #8498 — Open — sin historia

Caso 5 — Hypothesis #4854 — **Closed** — cerrado por política AI, respondí, sin reapertura

Caso 6 — spec-kit #4106 — Open — **respondido; preset publicado, hoy en `v1.0.1`; catálogo submiteado (PR #4179) e issue de submission (#4192); PR de un tercero (#4160) con cambios pedidos; afirmación corregida el 18-ago**

Caso 7 — eslint #21223 — Open — labels `core` y `feature`; **un colaborador se ofrece a implementarlo, decisión pendiente mía**

Caso 8 — phpstan #15078 — **Closed (completed)** — respuesta hostil, respondí, mantenedor volvió con respuesta técnica sustantiva y cerró

Caso 9 — rubocop #15570 — Open — sin historia

Caso 10 — eslint #21226 — Open — sin historia (es el issue-origen citado por los demás)

Caso 11 — SwiftLint #6871 — Open — **confirmado + PR de fix (#6872) por un colaborador; le respondí en la PR con el residuo del grupo completo**

Caso 12 — golangci-lint #6714 — **Closed (declined)** — declined, respondí, sin reapertura

Caso 13 — stylelint #9438 — Open — **retitulado y aceptado por el mantenedor como pedido de función**, esperando a ESLint

Caso 14 — oxc discussion #22198 — comentario publicado — sin historia (1 like, sin reply)

Caso 15 — Reporte de seguridad MSRC — **caso abierto, bajo reserva** — detalles no publicados hasta el fin del período de disclosure

Caso 16 — bandit #1467 — Open — sin historia

Caso 17 — checkov #7647 — Open — sin historia

Caso 18 — diff_cover #619 — **Closed (completed)** — cerrado sin comentario ni PR/commit vinculado

Caso 19 — pa11y-ci #352 — Open — sin historia

Caso 20 — lighthouse-ci #1145 — Open — sin historia

Caso 21 — eslint-seatbelt #30 — Open — sin historia

Caso 22 — codecov/umbrella #1989 — Open — sin historia

Caso 23 — gitleaks #2239 — Open — sin historia

— mypy-baseline — **bloqueado, sin issue** — vulnerable confirmado en vivo, repo no acepta issues públicas

Caso 24 — vulture #430 — Open — sin historia

Caso 25 — knip #1949 — Open — sin historia

Caso 26 — deptry #1654 — **Closed (not planned)** — "AI slop", respondí con tono neutro, sin reapertura

Caso 27 — jscpd #938 — Open — **mantenedor aceptó el planteo y publicó el diseño; le respondí con multiplicidad de fingerprint y regeneración no silenciosa**

Caso 28 — simian #23 — Open — sin historia

Caso 29 — audit-ci #356 — Open — sin historia

Caso 30 — pip-audit #1113 — **Closed (not planned)** — cerrado sin comentario

Caso 31 — safety #907 — Open — sin historia (1 comentario, solo bot de bienvenida)

Caso 32 — maven-pmd-plugin #724 — Open — sin historia (code-only)

Caso 33 — dependency-check #8751 — **Closed (not planned)** — cerrado sin comentario (code-only)
