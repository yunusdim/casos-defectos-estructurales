┌─────────────────────────────────────────────────────────────┐
│  PROMPT          Registro de sala — mantenedores OSS         │
│  DOMINIO         comunicación técnica, issue trackers        │
│  APLICA A        linters / static analysis pragmáticos       │
│  NO APLICA A     salas de spec/IA (ver §5)                   │
│  FUENTE          extraído empírico de 15 casos, esta sesión  │
└─────────────────────────────────────────────────────────────┘

# 0 · INVARIANTE DE ENTRADA

Esta plantilla no toca el contenido técnico. El hallazgo ya está reproducido
contra código real antes de escribir una sola línea. Lo único que cambia es
la superficie lingüística — la arquitectura del lenguaje de la sala, no la
verdad de lo que se reporta.

# 1 · EVIDENCIA (por qué esta plantilla y no otra)

De 15 casos filed con registro académico (formal result, separation
criterion, DOI, preámbulo narrativo):

  11/15   sin ninguna respuesta
  1/15    respuesta hostil, específicamente al registro
          (phpstan#15078 — ondrejmirtes: "unreadable AI slop")
  2/15    respuesta técnica neutra, ignorando el registro
          (golangci-lint#6714 declined con argumento técnico real;
           spec-kit#4106 — única sala que premió el registro largo)
  1/15    cerrado por violar política de disclosure de AI, no por registro
          (hypothesis#4854)

Señal clara en un solo caso (phpstan). El resto es compatible con volumen
de cola, no solo con tono — no se declara causalidad, se declara hipótesis
a testear. Ver §6.

# 2 · ESTRUCTURA FIJA (5 bloques, este orden, nada más)

  1  Title       verbo + objeto + consecuencia observable.
                 Sin adjetivo, sin metáfora, sin "silently" salvo que sea
                 literal (falla sin excepción, sin log, sin exit code).

  2  Repro       bloque de código/config mínimo. Primero. Sin prosa antes.
                 Numerado si son pasos.

  3  Actual/Expected   dos líneas, una cada una. Nada más entre ellas.

  4  Por qué importa   una frase. Escenario de daño concreto.
                 Sin cita a paper, sin "resultado formal", sin marco
                 teórico. Describe qué se cancela o qué se pierde.

  5  Suggest     una acción, imperativo. No "would you consider" —
                 "Suggest: X". Costo declarado si lo hay (ej: "no requiere
                 tracking permanente, solo diff en prune-time").
                 Link a la fuente, si corresponde, como última línea,
                 sin desarrollar — antecedente opcional, no autoridad.

# 3 · LÉXICO PROHIBIDO (fricción medida en esta sesión)

  "formal result"              "I deposited"
  "separation criterion"       "degeneration"
  "concrete question"          "Hi — an observation... not a bug report"
  cualquier preámbulo > 2 líneas antes del repro
  cualquier frase que declare la autoridad de la fuente antes del hecho

# 4 · LÉXICO PREFERIDO

  "breaks when"     "silently"        "doesn't detect"
  "same bucket"     "false negative"  "repro:"
  "expected:"       "actual:"         "suggest:"
# 5 · TRANSFORMACIÓN — antes/después (eslint#21226 como caso real)

ANTES (registro usado, académico — el que filed):
  "Hi — an observation about eslint-suppressions.json, not a bug report.
  It comes from a formal result on separation criteria I deposited
  recently, and I'm opening it here because..."
  [preámbulo, luego el repro, luego "Concrete question."]

DESPUÉS (registro de sala):

  Title: --prune-suppressions doesn't detect a same-count swap in one bucket

  Repro:
  1. eslint --suppress-all → count:2 for (file.js, no-unused-vars) [alpha, beta]
  2. fix alpha, introduce gamma (same rule, same file)
  3. eslint --prune-suppressions

  Actual: exit 0, no warning, nothing pruned.
  Expected: warn that the bucket's line set changed even though count(2)
  is unchanged.

  Why it matters: a real fix and a real regression of the same rule
  cancel out silently — the one command meant to audit this doesn't
  catch it either.

  Suggest: at prune time, diff current matching lines against the last
  recorded state; flag if count matches but lines differ. No permanent
  per-line tracking needed.

  (background, optional: https://doi.org/10.5281/zenodo.21908527)

# 6 · REGLA DE APLICACIÓN

Usar en: salas de linters/static-analysis pragmáticos — eslint, phpstan,
rubocop, golangci-lint, swiftlint, stylelint, oxc, ktlint, ArchUnit,
dependency-cruiser, import-linter, rewrite. Cultura: código antes que marco.

No usar igual en: spec-kit y salas de spec/IA — ahí mnriem respondió bien
al registro largo con antecedente formal. Esa sala espera ese marco, no
hay que aplanarlo.

Cada sala nueva: mirar 2-3 issues cerrados con respuesta de mantenedor
antes de escribir, y calibrar contra eso — no asumir una arquitectura de
lenguaje universal.

# 7 · CONTROL A/B YA DISPONIBLE

Los 13 issues ya filed con registro académico son el grupo de control.
Los próximos, escritos con este prompt, son el grupo de tratamiento.
Comparar tasa de respuesta de acá en adelante contra la tasa histórica
(2/13 con respuesta sustantiva, 1/13 hostil, 10/13 silencio) antes de
declarar que el registro fue la causa del silencio.
