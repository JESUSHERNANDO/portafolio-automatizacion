# Descripción funcional
## Sistema de control de nivel con bombeo de velocidad variable

**Proyecto:** Portafolio de automatización industrial
**Autor:** Jesús Hernando Pérez Maldonado
**Revisión:** 12 — agosto 2026
**Controlador:** SIMATIC S7-1500 · TIA Portal · WinCC Basic

### Control de revisiones

| Rev | Cambios |
|---|---|
| 0 | Emisión inicial |
| 1 | Tiempo mínimo entre arranques reducido de 300 a 60 s · Eliminación del estado RAMPA · Arquitectura de comunicación con el variador · Alarma AL-09 · Máquina de estados formalizada |
| 2 | Transiciones a FALLA especificadas con registro de primera causa · Órdenes de marcha y reconocimiento por flanco · Alarma AL-10 · Tabla de alarmas ampliada con tipo, rearme y código de causa · Definición del mecanismo de palabra de alarmas para el HMI |

---

## 1. Objeto

Controlar automáticamente el nivel de un tanque de proceso mediante la regulación
de velocidad de una bomba centrífuga, garantizando la protección del equipo de
bombeo y registrando las variables necesarias para el seguimiento de su condición.

## 2. Descripción del proceso

El fluido se almacena en una cisterna de succión (TK-01) desde donde una bomba
centrífuga (P-01), accionada por un motor con variador de frecuencia, lo impulsa
hacia el tanque de proceso (TK-02).

El consumo aguas abajo de TK-02 es variable y no controlado por este sistema: esa
es la perturbación principal del lazo. El sistema debe mantener el nivel de TK-02
en su valor de consigna ajustando la velocidad de la bomba, sin arrancar y parar
el motor de forma repetida.

**Datos de diseño**

| Elemento | Valor |
|---|---|
| Capacidad TK-01 (cisterna) | 10 m³ |
| Capacidad TK-02 (proceso) | 5 m³, altura útil 3 m |
| Bomba P-01 | Centrífuga, 20 m³/h nominales |
| Motor M-01 | 7,5 kW · 380 V · 3~ |
| Variador | SINAMICS G120 |
| Consigna de nivel | 70 % (ajustable 30–85 %) |
| Velocidad mínima de operación | 30 % |
| Velocidad de parada | 1 % |
| Tiempo mínimo entre arranques | 60 s |
| Tiempo de rampa de aceleración | 10 s (ajustable 5–30 s desde HMI) |
| Timeout de confirmación de marcha | 5 s |
| Timeout de aceleración a velocidad mínima | 15 s |
| Timeout de parada | 30 s |

## 3. Arquitectura de control

El controlador se comunica con el variador por bus de campo (Modbus TCP o
PROFINET), lo que permite parametrizarlo y leer sus variables internas sin
instrumentación adicional.

**El controlador escribe al variador:** consigna de velocidad, orden de marcha y
parada, tiempo de rampa de aceleración y desaceleración.

**El controlador lee del variador:** estado de listo, confirmación de marcha,
velocidad efectiva, corriente del motor, par estimado, temperatura del disipador,
código de falla y horas de operación acumuladas.

La rampa de aceleración la ejecuta el variador con su propia electrónica. El
controlador únicamente la parametriza. El tiempo de rampa es ajustable desde el
HMI dentro de límites acotados, para impedir que se configure un valor tan bajo
que equivalga a un arranque directo.

**Implementación por etapas.** La primera fase se implementa con señales
cableadas para disponer de un sistema funcional, y la segunda migra las señales
del variador a comunicación. La lista de entradas y salidas del apartado 11
corresponde a la primera fase.

## 4. Instrumentos y actuadores

| Tag | Descripción | Señal |
|---|---|---|
| LT-01 | Transmisor de nivel TK-02 | 4–20 mA, 0–100 % |
| LSL-01 | Nivel bajo-bajo en cisterna TK-01 | Discreta, contacto NC |
| FT-01 | Transmisor de caudal en descarga | 4–20 mA, 0–30 m³/h |
| IT-01 | Corriente del motor | Fase 1: 4–20 mA desde el variador · Fase 2: por comunicación |
| VLV-01 | Válvula manual de salida a proceso | Sin actuador, no controlada |

## 5. Modos de operación

**Automático.** El regulador PID mantiene el nivel de TK-02 en la consigna,
actuando sobre la velocidad de la bomba. Es el modo normal de servicio.

**Manual.** El operador fija directamente la velocidad de la bomba desde el HMI.
Destinado a mantenimiento y puesta en marcha. Los enclavamientos de seguridad
permanecen activos en este modo. El operador puede modificar la consigna de nivel
y la velocidad, pero no los parámetros de sintonía del PID.

**Fuera de servicio.** Bomba parada por orden del operador o por enclavamiento.

El cambio entre automático y manual es sin salto: al pasar a manual, la consigna
de velocidad se inicializa con el último valor de salida del PID; al volver a
automático, el PID se inicializa con la velocidad actual. Con esto se evita un
escalón en la velocidad del motor durante la transición.

## 6. Máquina de estados

| # | Estado | Función |
|---|---|---|
| 0 | REPOSO | Todo apagado, esperando orden de marcha |
| 1 | ESPERA_REARRANQUE | Temporiza el tiempo mínimo desde la última parada |
| 2 | ARRANCANDO | Orden dada al variador, esperando confirmación y velocidad mínima |
| 3 | *(reservado)* | Ver decisión de diseño 12.2 |
| 4 | REGULANDO | PID en control. Estado normal de servicio |
| 5 | MANUAL | El operador fija la velocidad |
| 6 | PARANDO | Rampa descendente |
| 9 | FALLA | Bloqueado, requiere reconocimiento |

### 6.1 Transiciones de operación

| Desde | Hacia | Condición | Acción al entrar |
|---|---|---|---|
| 0 | 1 | **Flanco** de orden de marcha ∧ permisivos OK | Arrancar temporizador de rearranque |
| 1 | 0 | Orden de parada o cancelación | Reiniciar temporizador |
| 1 | 2 | Temporizador de rearranque cumplido | Escribir consigna mínima y dar orden de marcha al variador |
| 2 | 4 | Confirmación de marcha ∧ velocidad ≥ 30 % | Habilitar PID, inicializar sin salto |
| 4 | 5 | Selector a manual | Inicializar consigna manual con la salida del PID |
| 5 | 4 | Selector a automático | Inicializar PID con la velocidad actual |
| 4 | 6 | Orden de parada del operador | Consigna a cero, iniciar rampa descendente |
| 5 | 6 | Orden de parada del operador | Consigna a cero, iniciar rampa descendente |
| 6 | 0 | Velocidad ≤ velocidad de parada | Retirar habilitación, registrar hora de parada |
| 9 | 0 | **Flanco** de reconocimiento ∧ sin enclavamientos activos | Limpiar código de causa |

### 6.2 Transiciones a FALLA

Los enclavamientos se evalúan **antes** del bloque de estados, de modo que la
pérdida de un permisivo retire las salidas en el mismo ciclo de scan en que se
detecta. Actúan desde cualquier estado operativo.

| Desde | Condición | Causa registrada |
|---|---|---|
| Cualquier estado ≠ 0 y ≠ 9 | Pérdida de cualquier permisivo | Primera causa detectada |
| 2 | Timeout de confirmación (5 s) sin marcha del variador | AL-05 |
| 2 | Con marcha confirmada, timeout de aceleración (15 s) sin alcanzar velocidad mínima | AL-09 |
| 6 | Timeout de parada (30 s) sin que la velocidad descienda | AL-10 |

Al entrar en FALLA se reinician todos los temporizadores del estado abandonado.

### 6.3 Registro de primera causa

Cuando se produce una parada, las condiciones anormales suelen presentarse en
cascada: la pérdida de un permisivo provoca que el variador reporte falla, y
ambas quedan activas simultáneamente. Para que el diagnóstico sea útil, el
sistema registra únicamente la **primera** condición detectada y la conserva
hasta el reconocimiento.

El código de causa coincide con el número de la alarma correspondiente, de modo
que existe un único mapeo entre lógica y visualización.

### 6.4 Órdenes por flanco y por nivel

Las órdenes que **activan** el sistema se evalúan por flanco de subida: marcha y
reconocimiento de alarmas. Las órdenes que **detienen** se evalúan por nivel:
parada y enclavamientos.

El criterio es de diseño a prueba de fallos. Una orden de marcha evaluada por
nivel provocaría el rearranque automático del equipo si la señal permanece
activa tras un reconocimiento, con el riesgo de un arranque inesperado durante
una intervención de mantenimiento. Un fallo en la detección de flanco de la
orden de marcha se traduce en un arranque perdido; el mismo fallo aplicado a la
orden de parada se traduciría en una parada perdida.

## 7. Enclavamientos y protecciones

Los enclavamientos provocan parada inmediata sin rampa, a diferencia de la
parada ordenada por el operador.

| Condición | Acción | Rearme |
|---|---|---|
| Parada de emergencia | Parada inmediata | Manual |
| Nivel bajo-bajo en cisterna (marcha en seco) | Parada inmediata | Automático con retardo de 60 s |
| Nivel alto-alto en TK-02 (95 %) | Parada inmediata | Automático al bajar de 90 % |
| Falla del variador | Parada inmediata | Manual |
| Corriente del motor fuera de rango | Parada inmediata | Manual |

La protección contra marcha en seco tiene prioridad sobre cualquier otra orden,
incluido el modo manual. Una bomba centrífuga operando sin fluido destruye el
sello mecánico en cuestión de minutos.

## 8. Alarmas

| Código | Descripción | Prioridad | Tipo | Rearme | Código de causa |
|---|---|---|---|---|---|
| AL-01 | Parada de emergencia accionada | Alta | Disparo | Manual | 1 |
| AL-02 | Nivel bajo-bajo en cisterna | Alta | Disparo | Automático, 60 s | 2 |
| AL-03 | Nivel alto-alto en TK-02 | Alta | Disparo | Automático al bajar de 90 % | 3 |
| AL-04 | Falla del variador | Alta | Disparo | Manual | 4 |
| AL-05 | Falla de arranque, sin confirmación del variador | Alta | Disparo | Manual | 5 |
| AL-06 | Corriente del motor fuera de rango | Alta | Disparo | Manual | 6 |
| AL-07 | Desviación de nivel sostenida (>10 % por 120 s) | Media | Aviso | Automático | — |
| AL-08 | Mantenimiento programado vencido | Baja | Aviso | Manual | — |
| AL-09 | La bomba no alcanza la velocidad mínima | Alta | Disparo | Manual | 9 |
| AL-10 | Parada no completada en el tiempo previsto | Alta | Disparo | Manual | 10 |

**Tipo de alarma.** Las alarmas de *disparo* conducen al estado FALLA y detienen
el equipo. Las de *aviso* se anuncian al operador sin interrumpir la operación:
señalan una condición que requiere atención pero no compromete la integridad del
equipo de forma inmediata.

Todas las alarmas requieren reconocimiento del operador. La condición se registra
con marca de tiempo de aparición, reconocimiento y desaparición.

En la fase 2, las alarmas AL-04 y AL-09 se enriquecen con el código de falla
específico reportado por el variador, lo que permite distinguir la causa concreta
en lugar de inferirla.

### 8.1 Señalización de alarmas al HMI

El sistema mantiene dos registros distintos, que responden a preguntas distintas:

| Registro | Pregunta que responde | Formato |
|---|---|---|
| Código de causa | ¿Qué detuvo el equipo? | Entero, primera causa |
| Palabra de alarmas | ¿Qué condiciones están activas ahora? | Palabra de bits, una por alarma |

El código de causa conserva un único valor hasta el reconocimiento, de modo que
el diagnóstico apunte al origen de la parada. La palabra de alarmas refleja el
estado instantáneo de todas las condiciones, y es la que alimenta la lista de
alarmas del HMI, donde el operador debe ver simultáneamente todo lo que está
fuera de rango.

## 9. Diagnóstico diferenciado de fallos de arranque

El estado ARRANCANDO vigila dos condiciones sucesivas mediante temporizadores de
entradas complementarias: mientras el variador no confirma marcha corre el
temporizador de confirmación, y en cuanto confirma se reinicia y arranca el de
aceleración. La conmutación no requiere lógica adicional.

| Alarma | Interpretación | Área a inspeccionar |
|---|---|---|
| AL-05 | El variador no respondió a la orden de marcha | Eléctrica: alimentación, cableado de habilitación, estado del variador |
| AL-09 | El variador confirmó marcha pero el conjunto no alcanza régimen | Mecánica: impulsor obstruido, sobrecarga, agarrotamiento de rodamientos |

La distinción reduce el tiempo de diagnóstico: cada alarma dirige al técnico a un
área de inspección distinta. El temporizador de aceleración se cuenta desde la
confirmación de marcha y no desde el inicio del estado, de modo que el tiempo
medido corresponde efectivamente a la aceleración del conjunto.

## 10. Monitoreo de condición

| Indicador | Utilidad |
|---|---|
| Horas de operación acumuladas | Programación de mantenimiento preventivo |
| Número de arranques | El arranque es el evento de mayor desgaste del motor |
| Corriente del motor y su tendencia | Sobrecarga mecánica, desgaste de rodamientos |
| Relación caudal / velocidad | **Indicador de degradación del impulsor** |
| Desviación media del lazo | Deterioro del control o de la instrumentación |
| Par estimado y temperatura del variador | Disponibles en fase 2 por comunicación |

La relación caudal/velocidad es el indicador más informativo del conjunto: si a
igual velocidad la bomba entrega progresivamente menos caudal, hay desgaste de
impulsor, obstrucción o recirculación interna. Es una medida de degradación
obtenida sin instrumentación adicional.

## 11. Lista de entradas y salidas — fase 1

**Entradas digitales**

| Dirección | Tag | Descripción |
|---|---|---|
| I0.0 | HS_Marcha | Orden de marcha |
| I0.1 | HS_Parada | Orden de parada |
| I0.2 | HS_Emergencia | Seta de emergencia (NC) |
| I0.3 | LSL_01 | Nivel bajo-bajo cisterna (NC) |
| I0.4 | VFD_Listo | Variador listo, sin falla |
| I0.5 | VFD_Marcha | Confirmación de marcha |
| I0.6 | HS_Reset | Reconocimiento de alarmas |

**Salidas digitales**

| Dirección | Tag | Descripción |
|---|---|---|
| Q0.0 | VFD_Habilitacion | Habilitación del variador |
| Q0.1 | HL_Marcha | Señalización de marcha |
| Q0.2 | HL_Alarma | Señalización de alarma |

**Entradas analógicas**

| Dirección | Tag | Descripción | Rango |
|---|---|---|---|
| IW64 | LT_01 | Nivel TK-02 | 0–100 % |
| IW66 | FT_01 | Caudal de descarga | 0–30 m³/h |
| IW68 | IT_01 | Corriente del motor | 0–20 A |

**Salidas analógicas**

| Dirección | Tag | Descripción | Rango |
|---|---|---|---|
| QW64 | SC_VFD | Consigna de velocidad | 0–100 % |

**Total fase 1: 7 ED · 3 SD · 3 EA · 1 SA**

En fase 2, las señales VFD_Listo, VFD_Marcha, VFD_Habilitacion, IT_01 y SC_VFD
migran a comunicación, reduciendo el cableado a 5 ED · 2 SD · 2 EA · 0 SA.

## 12. Decisiones de diseño

**12.1 · Tiempo mínimo entre arranques: 60 s, no 300 s.**
Los valores habituales de 300 a 360 s para motores de 5 a 15 kW corresponden a
arranque directo, donde la corriente alcanza de 6 a 8 veces la nominal y el
bobinado necesita disipar ese calor. Con arranque mediante variador la corriente
se mantiene en torno a 1,1–1,5 veces la nominal, por lo que la restricción
térmica deja de ser el criterio limitante. El temporizador se conserva por el
desgaste mecánico del sello, que trabaja en seco durante los primeros instantes
de cada arranque, y por los transitorios de presión en la tubería de descarga.

**12.2 · Eliminación del estado RAMPA.**
Se evaluó un estado dedicado en el que el controlador generase la consigna de
velocidad de forma incremental. Se descartó porque la rampa la ejecuta el
variador con mejor resolución y respuesta, y porque duplicar en software una
función que el hardware ya realiza introduce una dependencia innecesaria: la
rampa del variador sigue actuando aunque el controlador falle. El número 3 se
mantiene reservado para no renumerar la máquina de estados.

**12.3 · Parametrización del variador por comunicación.**
El tiempo de rampa se escribe al variador desde el controlador por bus de campo,
lo que permite ajustarlo desde el HMI sin intervenir el equipo ni utilizar el
software del fabricante. El mismo enlace se aprovecha para leer corriente, par,
temperatura y códigos de falla, lo que enriquece el monitoreo de condición y
elimina un canal analógico. Al implementarlo debe verificarse que el tiempo de
aceleración interno del variador quede en su valor mínimo, para evitar que ambas
rampas se sumen y el arranque resulte más lento que el configurado.

**12.4 · Distinción entre parada ordenada y enclavamiento.**
La orden de parada del operador conduce al estado PARANDO y ejecuta una rampa
descendente. Un enclavamiento conduce al estado FALLA y retira la habilitación de
inmediato. Rampar ante una emergencia o ante una falla del variador sería, según
el caso, peligroso o imposible.

**12.5 · Transición sin salto entre modos.**
Sin inicialización cruzada, el paso de manual a automático haría que el regulador
partiese de su condición inicial y produjese un escalón en la velocidad del
motor.

**12.6 · Evaluación de enclavamientos antes del bloque de estados.**
Situar la comprobación de permisivos por delante del bloque de estados permite
que la transición a FALLA y el retiro de salidas ocurran en el mismo ciclo de
scan en que se detecta la condición. Evaluándola después, el estado activo
alcanzaría a ejecutar sus acciones y las salidas se retirarían un ciclo más
tarde.

**12.7 · Registro de primera causa en lugar de última.**
Conservar la primera condición detectada permite identificar el origen de una
parada en cascada. Registrar la última mostraría al operador la consecuencia en
lugar de la causa.

**12.8 · Órdenes de activación por flanco.**
Ver apartado 6.4.

**12.9 · Temporizadores complementarios en el estado ARRANCANDO.**
Ver apartado 9.

**12.10 · La parada por timeout se trata como falla.**
Cuando el equipo se detiene por vencimiento del temporizador de parada y no por
descenso efectivo de la velocidad, el sistema conduce a FALLA en lugar de a
REPOSO. Aunque la habilitación se retira en ambos casos, la parada por timeout
indica que la señal de velocidad o el propio variador no responden, y permitir un
rearranque inmediato supondría operar sobre un equipo con un fallo sin
diagnosticar.

## 13. Fuera de alcance

Se excluyen de esta revisión, y quedan identificados como ampliación posible:

- Operación en configuración dúplex con alternancia de bombas
- Modo de velocidad mínima permanente en lugar de parada, para eliminar los
  ciclos de arranque cuando no hay demanda
- Separación entre disparo inmediato y parada controlada por alarma, con un
  estado intermedio que ejecute rampa descendente antes de bloquear
- Registro histórico en base de datos externa
- Clasificación automática de fallas a partir de señales de vibración
- Señalización sonora de alarma en campo
---

## Las tres reglas

1. **Los entregables no se negocian, el calendario sí.** Si algo toma diez semanas en vez de ocho, no pasa nada. Si terminas ocho semanas sin video, sí.
2. **Construir vale más que ver.** Si te sobran horas, programa algo. No veas a alguien programar.
3. **Nada con corchetes se publica.** Antes de cada commit, busca `[` en el editor.
