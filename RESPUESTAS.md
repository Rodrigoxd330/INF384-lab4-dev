# Respuestas · Laboratorio 4
Alumno: [20210948]
Presupuesto: [ 8 ] min  Métrica DORA: [ Change Failure Rate ]

## Parte 1 · Diagnóstico
### Defecto 1
- Job y etapa: deploy
- Problema que causa en el pipeline: El pipeline se ejecuta en cada PR lo que hace que PRs aún no mergeadas salgan a producción
- Link al commit:
- Corrección (una o más: mover de etapa / dejar como informativo / eliminar / ajustar configuración / modificar tests): Bloquear 
- Evidencia (dato observado, con link al run o fragmento de la salida local): Una pipeline ejecutado por una PR que en el cual 'deploy' se ejecute.
### Defecto 2
- Job y etapa: Smoke
- Problema que causa en el pipeline: Smoke y Deploy llegan a ejecutarse en paralelo, lo cual indica que Smoke apunta a la versión anterior.
- Link al commit:
- Corrección (una o más: mover de etapa / dejar como informativo / eliminar / ajustar configuración / modificar tests): Hacae que smoke y deploy sean secuenciales
- Evidencia (dato observado, con link al run o fragmento de la salida local): Ver en el pipeline que smoke termine antes de deploy, esto indica que se está leyendo la versión anterior
### Defecto 3
- Job y etapa: integration
- Problema que causa en el pipeline: Los test no son independientes entre si. Test 2 de integració asume que test 1 ya ha sido ejecutado lo que puede llevar a dar falsos negativos.
- Link al commit:
- Corrección (una o más: mover de etapa / dejar como informativo / eliminar / ajustar configuración / modificar tests): Resetear la BD antes de cada test
- Evidencia (dato observado, con link al run o fragmento de la salida local): Al ejecutar solo uno de los test se puede llegar a obtner el falso negativo.
### Defecto 4
- Job y etapa:
- Problema que causa en el pipeline:
- Link al commit:
- Corrección (una o más: mover de etapa / dejar como informativo / eliminar / ajustar configuración / modificar tests):
- Evidencia (dato observado, con link al run o fragmento de la salida local):
### Defecto 5
- Job y etapa:
- Problema que causa en el pipeline:
- Link al commit:
- Corrección (una o más: mover de etapa / dejar como informativo / eliminar / ajustar configuración / modificar tests):
- Evidencia (dato observado, con link al run o fragmento de la salida local):

## Parte 2 · Implementación
- Link al run en verde en main:
- Link al pull request:
- Defecto de la aplicación introducido (archivo y descripción):
- Job que debería detenerlo:
- Link al run fallido:

## Parte 3 · Decisión bajo restricción
Link al run medido:

| Job | Duración (min:seg) |
|---|---|
| unit | |
| integration | |
| sast | |
| sca | |
| build-push | |
| deploy | |
| smoke | |
| e2e | |
| load | |
| **Total** | |

Resultado de load en ese run:
| Métrica | Valor |
|---|---|
| p90 | |
| p95 | |
| Máximo | |
| Tasa de error | |
| Requests por segundo | |

Conclusión sobre el comportamiento bajo carga:

SLO propuesto (percentil, valor, ventana) y justificación:

SLA propuesto y diferencia con el SLO:

Cambios para cumplir el presupuesto:
| Cambio | Job | Minutos recuperados |
|---|---|---|

Total resultante: [ ] min
Link al run en main con los cambios:

Riesgo aceptado (defecto de la aplicación concreto y efecto en la métrica DORA asignada):

Decisión sobre sast (se queda / se mueve a otra etapa o evento / informativo) y razón:

## Declaración de uso de IA
Herramientas usadas y para qué:
