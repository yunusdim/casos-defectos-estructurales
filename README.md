# Casos de Defectos Estructurales

Registro de defectos "count-vs-identity" encontrados en herramientas OSS de
verificación (linters, baselines de type-checking, detectores de código
duplicado, scanners de secretos, gates de vulnerabilidades de dependencias,
etc.): mecanismos de baseline/supresión/exclusión que trackean un agregado
(count, porcentaje, ID desnudo, nombre desnudo) por bucket en lugar de la
identidad completa de la instancia (archivo+línea+hash de contenido, o
ID+paquete+versión+ruta), de modo que un swap del mismo tamaño (arreglo real
+ regresión real) es invisible para el gate.

Cada caso documenta: repro (en vivo o solo por código, declarado
explícitamente), el issue filed en el upstream correspondiente, y el link.

Metodología de reporte: ver `prompt-registro-sala.md`.

Trabajo relacionado: [Closure Trivialization](https://doi.org/10.5281/zenodo.21908527)
(Diego Gabriel Impieri).
