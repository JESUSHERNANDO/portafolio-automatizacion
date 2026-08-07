# Portafolio de Automatización Industrial

Estudiante de Ingeniería Electromecánica enfocado en automatización industrial y Diseño en Monitoreo para mantenimiento Predictivo:
PLC Siemens, HMI y programación bajo IEC 61131-3.

📍 Tibú - Norte De Santander, Colombia · [LinkedIn](https://www.linkedin.com/in/jes%C3%BAs-hernando-perez-651948269/) · jesushernando2027@gmail.com

---

## Competencias técnicas

| Área | Herramientas |
|---|---|
| Programación PLC | TIA Portal (STEP 7 Professional), CODESYS |
| Lenguajes | KOP/Ladder, SCL / Texto Estructurado, GRAFCET |
| HMI | WinCC Basic |
| Simulación | PLCSIM, Factory I/O |
| Comunicaciones | PROFINET, Modbus TCP |

---

## Proyectos
# Sistema de envasado automatizado

## Descripción del proceso
[Qué hace la máquina, en tres o cuatro líneas]

## Alcance implementado
- Secuencia automática de llenado, tapado y evacuación
- Modo manual para mantenimiento con enclavamientos
- Gestión de alarmas con reconocimiento
- HMI de operación con sinóptico animado

## Arquitectura
| Elemento | Detalle |
|---|---|
| CPU | S7-1200 CPU 1214C DC/DC/DC |
| HMI | KTP700 Basic |
| Software | TIA Portal V20 + PLCSIM |

## Lista de entradas y salidas
| Dirección | Tag | Descripción | Tipo |
|---|---|---|---|
| I0.0 | S_Marcha | Pulsador de marcha | Bool |
| Q0.0 | K_Banda | Contactor banda transportadora | Bool |

## Estructura del programa
- `OB1` — llamada cíclica
- `FB_Secuencia` — máquina de estados del ciclo
- `FC_Alarmas` — gestión y enclavamiento de alarmas
- `DB_Parametros` — tiempos y consignas

## Decisiones de diseño
[Por qué máquina de estados y no lógica secuencial. Por qué un FB con instancia
y no un FC. Cómo resolviste el paso de manual a automático sin arranques
inesperados.]

## Video
[Enlace a YouTube]

