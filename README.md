# Casos de defectos estructurales

Registro publico de defectos estructurales reportados en herramientas y
runtimes de codigo abierto. 43 casos, cada uno con su issue en el upstream,
su reproduccion y su estado.

No es una lista de bugs. Es el mismo defecto encontrado 43 veces en 43
proyectos distintos, en dos formas.

## Primera linea: contar en vez de identificar

Casos 1 a 33. Linters, baselines de type-checking, detectores de codigo
duplicado, scanners de secretos, gates de vulnerabilidades de dependencias.

El mecanismo de baseline, supresion o exclusion trackea un agregado por
bucket: un numero, un porcentaje, un ID pelado, un nombre pelado. No la
identidad completa de la instancia, que seria archivo + linea + hash del
contenido, o ID + paquete + version + ruta.

La consecuencia: un swap del mismo tamano pasa invisible. Se arregla un
defecto real, entra una regresion real, el conteo no se mueve y el gate da
verde. La herramienta informa que nada cambio. Cambiaron dos cosas.

## Segunda linea: no poder decir que entro

Casos 34 a 43. Runtimes que arman el contexto de un modelo de lenguaje:
frameworks de agentes, proxies entre proveedores, capas de memoria, CLIs.

El sistema ensambla lo que le manda al modelo y despues no puede declarar
que entro y que quedo afuera. Trunca sin avisar. Descarta un campo que no
reconoce y devuelve 200. Sirve un modelo distinto del pedido y atribuye el
resultado al pedido. Una seccion ausente y una seccion vacia se ven igual.

Es la misma falla que la primera linea, un nivel mas arriba: el sistema
reporta un agregado y no puede reconstruir la identidad de lo que hizo.

## Que hay en cada caso

Reproduccion, declarada como en vivo o solo por lectura de codigo. El issue
filed en el upstream, con link. El estado y la historia: que respondio el
proyecto, que se corrigio, que se rechazo y por que.

El indice del final tiene los 43, caso por caso.

## Estado al 9 de septiembre de 2026

43 casos indexados, 33 en la primera linea y 10 en la segunda.
11 cerrados, 30 abiertos, 28 con respuesta registrada del proyecto.

El proyecto escribio y publico el cambio:

- kucherenko/jscpd #938. El mantenedor publico v5.1.0 con el baseline de
  clones el 30-ago y cerro el issue trece minutos despues.
- google-gemini/gemini-cli #28859. El mantenedor abrio la PR #29252, la
  mergeo y cerro el issue el 8-sep, veintiuna horas despues del comentario.

El proyecto mergeo mi parche:

- seddonym/import-linter #375. PR #376, mergeada por el mantenedor el
  24-ago. La funcion entra al proyecto.

Escrito por un tercero, sin poder entrar:

- PyCQA/bandit #1467. Un tercero reprodujo el caso y escribio el fix. El
  repo no acepta PRs externas.

Aceptado y todavia sin construir:

- webpro-nl/knip #1949. Aceptado y derivado a #1532, que sigue abierto.
- stylelint/stylelint #9438. Retitulado y aceptado como pedido de funcion.
- eslint/eslint #21223. Etiquetado core y feature, con un colaborador
  ofreciendose a implementarlo.
- github/spec-kit #4106. Preset propio publicado, hoy en v1.0.1.

Los cierres sin cambio tambien estan, con el motivo.

## Movimientos al 16 de septiembre de 2026

Desde el censo del 9 de septiembre, verificado en vivo contra cada hilo. El
censo agregado de arriba (43 indexados, 33 y 10) sigue siendo el ultimo
recuento completo; lo de aca son los cambios de estado sobre esos casos, mas
la circulacion de segunda linea al final.

El proyecto escribio y publico el cambio:

- sverweij/dependency-cruiser #1078. El owner publico la v18.3.0 con las tres
  piezas: conteo de delta al actualizar la baseline (total, nuevas, existentes,
  removidas), un modo shrink-only que no admite violaciones nuevas, y el flag
  --baseline-shrink-only. PRs #1079, #1085 y #1088.

Aceptado y en el catalogo:

- github/spec-kit #4106. El preset closed-vocabulary quedo mergeado en el
  catalogo de la comunidad (PR #4201). Ademas hay un PR de un tercero, #4160,
  que lleva el mismo chequeo al analyze del core.

Un tercero escribio el fix, en revision:

- realm/SwiftLint #6871. LizunovSergey escribio el PR #6872 con el enfoque de
  ancla; espera aprobacion del mantenedor.

El mantenedor abrio el fix, en progreso:

- huggingface/transformers #29279. Rocketknight1 abrio el PR #47386 de
  sanitizacion del input de chat. La discusion converge en tokenizar el
  contenido no confiable aparte, para que no pueda producir los ids
  estructurales del modelo.

Cerrado con respuesta de fondo:

- phpstan/phpstan #15078. El mantenedor confirmo que la granularidad actual es
  un compromiso deliberado y derivo a sarb para el seguimiento por linea.

Segunda linea, en circulacion:

- El paper Engine Provenance llevo el caso de sustitucion silenciosa de modelo
  a mas runtimes: bifrost #6742, oh-my-pi #10294, goose #11041,
  claude-code-router #1787 y claude-code #81562. El campo de procedencia
  faltante quedo abierto como issue propio en simonw/llm #1681. Cada hilo se
  reduce a lo mismo: un motor respondio y el runtime atribuyo el turno al modelo
  pedido, no al que corrio. Estas son intervenciones sobre hilos de terceros
  mas el issue propio en simonw/llm, no casos nuevos del indice.

## Trabajo relacionado

Closure Trivialization in Typed Separation Systems
https://doi.org/10.5281/zenodo.21908527

Rick App: Fail-Closed Context Governance in the Browser
https://doi.org/10.5281/zenodo.22654842

Engine Provenance: Deterministic admission control over silent model
substitution in LLM runtimes
https://doi.org/10.5281/zenodo.22722523

Diego Gabriel Impieri
https://orcid.org/0009-0003-9082-650X
