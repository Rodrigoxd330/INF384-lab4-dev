# Respuestas · Laboratorio 4
Alumno: [20210948]
Presupuesto: [ 8 ] min  Métrica DORA: [ Change Failure Rate ]

## Parte 1 · Diagnóstico
### Defecto 1
- Job y etapa: deploy
- Problema que causa en el pipeline: El pipeline se ejecuta en cada PR lo que hace que PRs aún no mergeadas salgan a producción
- Link al commit: https://github.com/Rodrigoxd330/INF384-lab4-dev/commit/2849120a731bcda466e31fe9c1845f57e5e8d811
- Corrección (una o más: mover de etapa / dejar como informativo / eliminar / ajustar configuración / modificar tests): Bloquear 
- Evidencia (dato observado, con link al run o fragmento de la salida local): Una pipeline ejecutado por una PR que en el cual 'deploy' se ejecute.
### Defecto 2
- Job y etapa: Smoke
- Problema que causa en el pipeline: Smoke y Deploy llegan a ejecutarse en paralelo, lo cual indica que Smoke apunta a la versión anterior.
- Link al commit: https://github.com/Rodrigoxd330/INF384-lab4-dev/commit/aea0b003d4ed51cbc7b36c9c373da8e033fb35c0
- Corrección (una o más: mover de etapa / dejar como informativo / eliminar / ajustar configuración / modificar tests): Hacae que smoke y deploy sean secuenciales
- Evidencia (dato observado, con link al run o fragmento de la salida local): Ver en el pipeline que smoke termine antes de deploy, esto indica que se está leyendo la versión anterior
### Defecto 3
- Job y etapa: integration
- Problema que causa en el pipeline: Los test no son independientes entre si. Test 2 de integració asume que test 1 ya ha sido ejecutado lo que puede llevar a dar falsos negativos.
- Link al commit: https://github.com/Rodrigoxd330/INF384-lab4-dev/commit/3e0dcc0b229d1c7948caa33283835b23f798ecf9 y https://github.com/Rodrigoxd330/INF384-lab4-dev/commit/ed14a1825a452e3fe0d73cfe0284c7a62c587c3c
- Corrección (una o más: mover de etapa / dejar como informativo / eliminar / ajustar configuración / modificar tests): Resetear la BD antes de cada test
- Evidencia (dato observado, con link al run o fragmento de la salida local): Al ejecutar solo uno de los test se puede llegar a obtner el falso negativo.
### Defecto 4
- Job y etapa: smoke
- Problema que causa en el pipeline: La variable EXPECTED_COMMIT no es definida en ningún momento por lo que las validaciones no son ejecutadas.
- Link al commit: https://github.com/Rodrigoxd330/INF384-lab4-dev/commit/b1292ed37e021f6f250d36087aac4f1619306b50
- Corrección (una o más: mover de etapa / dejar como informativo / eliminar / ajustar configuración / modificar tests): Definir la variable expected commit
- Evidencia (dato observado, con link al run o fragmento de la salida local): Al ejecutar el pipeline se puede ver en los logs que los pasos debajo de la condicional nunca son ejecutados.
### Defecto 5
- Job y etapa: load
- Problema que causa en el pipeline: No se está midiendo correctamente el tiempo de respuesta, pues se toma un valor global para las ejecuciones (sumándose entre si)
- Link al commit: https://github.com/Rodrigoxd330/INF384-lab4-dev/commit/92ddf5c825c0d9a0003badeeaee686fb11026494
- Corrección (una o más: mover de etapa / dejar como informativo / eliminar / ajustar configuración / modificar tests): Medir tiempo por separado
- Evidencia (dato observado, con link al run o fragmento de la salida local): Se puede evidenciar en los logs que el tiempo de medición es uno solo cuando hay más de 1 request.

## Parte 2 · Implementación
- Link al run en verde en main: https://github.com/Rodrigoxd330/INF384-lab4-dev/actions/runs/40967157751
- Link al pull request: https://github.com/Rodrigoxd330/INF384-lab4-dev/pull/1
- Defecto de la aplicación introducido (archivo y descripción): wallte.js -> Línea 62, se modificó la función de transferir para que se transfiera una cantidad incorrecta siempre
- Job que debería detenerlo: Unit
- Link al run fallido: https://github.com/Rodrigoxd330/INF384-lab4-dev/actions/runs/40967953475

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
| p90 | 50.75ms|
| p95 | 52.48 ms|
| Máximo | 351.82 ms|
| Tasa de error | 0.00%|
| Requests por segundo | 50.61 req/s|

Conclusión sobre el comportamiento bajo carga:
Monedero Andino presenta un comportamiento estable bajo la carga evaluada, que alcanzó un máximo de 30 usuarios virtuales. Se procesaron 36 474 requests a una tasa aproximada de 50.61 requests por segundo sin registrar errores HTTP. La latencia fue baja y consistente: el p90 fue de 50.75 ms y el p95 de 52.48 ms. Además, los dos endpoints evaluados mostraron resultados muy similares, con un p95 de 52.33 ms para /saldo y 52.62 ms para /transferir.

El valor máximo de 351.82 ms indica que existieron algunas solicitudes considerablemente más lentas que el comportamiento habitual, pero estas fueron poco frecuentes y no afectaron significativamente a los percentiles p90 y p95. Por ello, bajo la carga probada, el sistema mantiene un tiempo de respuesta estable y no presenta señales de saturación.

SLO propuesto (percentil, valor, ventana) y justificación:
Se propone el siguiente SLO de latencia: p95 < 75 ms durante cada ejecución de la prueba de carga de 12 minutos.

SLA propuesto y diferencia con el SLO: 
Se propone como SLA para los clientes: p95 de latencia menor a 150 ms medido mensualmente.

Cambios para cumplir el presupuesto:
| Cambio | Job | Minutos recuperados |
|Mover la prueba de carga fuera del pipeline obligatorio de main y ejecutarla de forma independiente|load|12:00|

Total resultante: [ 2] min
Link al run en main con los cambios: https://github.com/Rodrigoxd330/INF384-lab4-dev/actions/runs/40969974883

Riesgo aceptado (defecto de la aplicación concreto y efecto en la métrica DORA asignada):
Al retirar load del pipeline obligatorio de main se acepta el riesgo de desplegar una regresión de rendimiento que no sea detectada por las demás pruebas. Un defecto concreto sería que una modificación en la lógica de /transferir aumente considerablemente su tiempo de respuesta, por ejemplo llevando su p95 desde los aproximadamente 52.62 ms observados hasta varios cientos de milisegundos, sin alterar el resultado funcional de la operación.

Decisión sobre sast (se queda / se mueve a otra etapa o evento / informativo) y razón:
Mantener SAST permite detectar vulnerabilidades de severidad ERROR antes de ejecutar build-push y deploy. Convertirlo en informativo permitiría que un hallazgo conocido no detenga el despliegue, y eliminarlo o ejecutarlo únicamente después del despliegue aumentaría el riesgo de que cambios defectuosos lleguen a producción.

## Declaración de uso de IA
Herramientas usadas y para qué:
Se usó la IA para poder entender el código y escribir código. No se usó para analizar ni buscar respuestas directas