# Portafolio de Automatización Industrial

Estudiante de Ingeniería Electromecánica enfocado en automatización industrial
y en el diseño de sistemas de monitoreo para mantenimiento predictivo.

**PLC Siemens · HMI WinCC · Programación bajo IEC 61131-3**

📍 Tibú, Norte de Santander, Colombia
🔗 [LinkedIn](https://www.linkedin.com/in/jes%C3%BAs-hernando-perez-651948269/)
📧 jesushernando2027@gmail.com

---

## Competencias técnicas

| Área | Herramientas |
|---|---|
| Programación PLC | TIA Portal (STEP 7 Professional), CODESYS |
| Lenguajes | KOP/Ladder, SCL / Texto Estructurado, GRAFCET |
| HMI | WinCC Basic |
| Simulación | PLCSIM, Factory I/O |
| Comunicaciones | PROFINET, Modbus TCP |
| Monitoreo de condición | Adquisición de variables, históricos y tendencias |

---

## Proyectos
# Descripción funcional
## Sistema de control de nivel con bombeo de velocidad variable

**Proyecto:** Portafolio de automatización industrial
**Autor:** Jesús Hernando Pérez Maldonado
**Revisión:** 0 — agosto 2026
**Controlador:** SIMATIC S7-1500 · TIA Portal · WinCC Basic

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

## 3. Instrumentos y actuadores

| Tag | Descripción | Señal |
|---|---|---|
| LT-01 | Transmisor de nivel TK-02 | 4–20 mA, 0–100 % |
| LSL-01 | Nivel bajo-bajo en cisterna TK-01 | Discreta, contacto NC |
| FT-01 | Transmisor de caudal en descarga | 4–20 mA, 0–30 m³/h |
| IT-01 | Corriente del motor, desde el variador | 4–20 mA, 0–20 A |
| SC-VFD | Consigna de velocidad al variador | 0–100 % |

## 4. Modos de operación

**Automático.** El regulador PID mantiene el nivel de TK-02 en la consigna,
actuando sobre la velocidad de la bomba. Es el modo normal de servicio.

**Manual.** El operador fija directamente la velocidad de la bomba desde el HMI.
Destinado a mantenimiento y puesta en marcha. Los enclavamientos de seguridad
permanecen activos en este modo.

**Fuera de servicio.** Bomba parada por orden del operador o por enclavamiento.

El cambio entre automático y manual es sin salto: al pasar a manual, la consigna
de velocidad se inicializa con el último valor de salida del PID; al volver a
automático, el PID se inicializa con la velocidad actual. Con esto se evita un
escalón en la velocidad del motor durante la transición.

## 5. Lógica de control

### 5.1 Secuencia de arranque

1. Verificación de condiciones de permiso: sin alarmas activas, variador listo,
   nivel de cisterna por encima del mínimo, emergencia liberada.
2. Cumplido el tiempo mínimo entre arranques (300 s desde la última parada).
3. Habilitación del variador.
4. Espera de confirmación de marcha (máx. 5 s). Sin confirmación, se declara
   falla de arranque.
5. Rampa de velocidad hasta el mínimo de operación (30 %).
6. Entrega del control al regulador PID.

### 5.2 Regulación

Lazo PID de acción inversa sobre el nivel de TK-02. La variable de proceso es
LT-01 escalada a porcentaje; la variable manipulada es la consigna de velocidad
del variador, limitada entre 30 % y 100 %.

El límite inferior no es arbitrario: por debajo del 30 % la bomba centrífuga no
genera altura suficiente para vencer la columna de descarga y entra en
recirculación, con calentamiento del fluido y desgaste sin trabajo útil.

### 5.3 Secuencia de parada

1. Rampa descendente hasta velocidad mínima.
2. Retiro de la habilitación del variador.
3. Registro de la hora de parada para el cómputo del tiempo mínimo entre
   arranques.

En parada por enclavamiento la habilitación se retira de inmediato, sin rampa.

## 6. Enclavamientos y protecciones

| Condición | Acción | Rearme |
|---|---|---|
| Parada de emergencia | Parada inmediata | Manual |
| Nivel bajo-bajo en cisterna (marcha en seco) | Parada inmediata | Automático con retardo de 60 s |
| Nivel alto-alto en TK-02 (95 %) | Parada inmediata | Automático al bajar de 90 % |
| Falla del variador | Parada inmediata | Manual |
| Falla de arranque (sin confirmación en 5 s) | Bloqueo de arranque | Manual |
| Corriente del motor fuera de rango | Parada inmediata | Manual |

La protección contra marcha en seco tiene prioridad sobre cualquier otra orden,
incluido el modo manual. Una bomba centrífuga operando sin fluido destruye el
sello mecánico en cuestión de minutos.

## 7. Alarmas

| Código | Descripción | Prioridad |
|---|---|---|
| AL-01 | Parada de emergencia accionada | Alta |
| AL-02 | Nivel bajo-bajo en cisterna | Alta |
| AL-03 | Nivel alto-alto en TK-02 | Alta |
| AL-04 | Falla del variador | Alta |
| AL-05 | Falla de arranque | Alta |
| AL-06 | Corriente del motor fuera de rango | Alta |
| AL-07 | Desviación de nivel sostenida (>10 % por 120 s) | Media |
| AL-08 | Mantenimiento programado vencido | Baja |

Todas las alarmas requieren reconocimiento del operador. La condición de alarma
se registra con marca de tiempo de aparición, reconocimiento y desaparición.

## 8. Monitoreo de condición

El sistema registra de forma continua los siguientes indicadores, orientados al
mantenimiento predictivo del conjunto motor-bomba:

| Indicador | Utilidad |
|---|---|
| Horas de operación acumuladas | Programación de mantenimiento preventivo |
| Número de arranques | El arranque es el evento de mayor desgaste del motor |
| Corriente del motor y su tendencia | Sobrecarga mecánica, desgaste de rodamientos |
| Relación caudal / velocidad | **Indicador de degradación del impulsor** |
| Desviación media del lazo | Deterioro del control o de la instrumentación |

La relación caudal/velocidad es el indicador más informativo del conjunto: si a
igual velocidad la bomba entrega progresivamente menos caudal, hay desgaste de
impulsor, obstrucción o recirculación interna. Es una medida de degradación
obtenida sin instrumentación adicional.

## 9. Lista preliminar de entradas y salidas

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

**Total: 7 ED · 3 SD · 3 EA · 1 SA**

---

## 10. Fuera de alcance

Se excluyen de esta revisión, y quedan identificados como ampliación posible:

- Operación en configuración dúplex con alternancia de bombas
- Comunicación con el variador por PROFINET en lugar de señales cableadas
- Registro histórico en base de datos externa
- Clasificación automática de fallas a partir de señales de vibración
### 1. Sistema de envasado con monitoreo de condición

`En desarrollo · entrega estimada octubre 2026`

Línea de llenado con secuencia automática, modo manual con enclavamientos y
gestión de alarmas, más una capa de monitoreo de condición: corriente de
motores, conteo de ciclos y tendencias históricas orientadas a mantenimiento
predictivo.

**Stack:** S7-1200 · TIA Portal V20 · WinCC Basic · PLCSIM

---

*Cada proyecto se publica con documentación funcional, lista de entradas y
salidas, código comentado y video de funcionamiento.*

