# Plan de trabajo — 8 semanas

**Jesús Hernando Pérez Maldonado**
Del 10 de agosto al 4 de octubre de 2026

**Objetivo:** ser contratable como junior de automatización Siemens antes de que termine la pasantía.
**Medida de éxito:** un video de 3 minutos, un repositorio documentado y una entrevista técnica que puedas sostener.

---

## Estado del arranque

**Listo**
- [x] Repositorio GitHub creado y README publicado
- [x] SITRAIN Freemium activo (Low-voltage controls, SINAMICS Converter, SIMATIC Automation Systems)
- [x] Coursera configurado, curso de inglés elegido, suscripción cancelada (acceso Plus hasta el 14 de agosto)
- [x] CODESYS instalado

**Pendiente antes del lunes**
- [x] Descargar TIA Portal (STEP 7 **Professional** + WinCC + PLCSIM) — **V20 y V21**, ambas versiones
- [x] Guardar los archivos sin ejecutar el instalador

---

## La decisión de la semana 1

Antes de programar nada hay que definir **qué proceso**. De ahí salen la lista de entradas y salidas, el HMI, el video y parte de la tesis.

### Recomendado: estación de bombeo con control de nivel

Tanque de almacenamiento alimentado por bomba con variador, control de nivel por PID, protecciones por nivel alto y bajo, y arranque/parada secuenciados.

**Por qué esta y no una envasadora:**

| Criterio | Ventaja |
|---|---|
| PID | Sale natural del proceso, no forzado como requisito de curso |
| Señales | Mezcla analógicas (nivel, caudal, corriente) con discretas (nivel alto/bajo, marcha, falla) |
| Mantenimiento predictivo | La bomba y su motor son **el** activo canónico del monitoreo de condición |
| Continuidad con la tesis | Corriente y vibración de bomba conectan directo con clasificación de fallas de rodamiento |
| Diferenciación | Menos común en portafolios que la línea de llenado |
| Sector | Encaja con petróleo, agua y proceso, no solo con manufactura discreta |

**Alcance de las 8 semanas:**
- Secuencia de arranque y parada con enclavamientos y temporización
- Control PID de nivel actuando sobre la velocidad del variador
- Modo manual para mantenimiento
- Alarmas: nivel alto, nivel bajo, falla de bomba, marcha en seco
- HMI con sinóptico animado, tendencia de nivel y ventana de alarmas
- Capa de monitoreo: horas de operación, número de arranques, corriente del motor, tendencia histórica

### Alternativas si prefieres otra
- **Línea de envasado:** más visual, más común, PID menos justificado
- **Estación de clasificación:** excelente en video, casi todo discreto, PID queda postizo

---

## Los 4 entregables

El calendario es negociable. Estos no.

1. **Proyecto estructurado** — OB/FB/FC/DB con direccionamiento simbólico
2. **Proceso completo en ladder** — secuencia, manual/automático, alarmas
3. **HMI animado en WinCC** — conectado y funcionando
4. **SCL + PID + una comunicación**

**Producto final:** video de 3 minutos + repositorio documentado.

**Orden de sacrificio si el tiempo aprieta:** primero cae la comunicación, después SCL, después el PID.
**El HMI no se cae nunca** — es requisito explícito de las vacantes.

---

## Calendario

### Semana 1 · 10–16 de agosto · 10 horas
*Sin reloj de licencia corriendo*

- [x] Decidir el proceso y escribir su descripción funcional en una página
- [x] Dibujar el P&ID simplificado a mano: tanque, bomba, sensores, actuadores
- [x] Lista preliminar de entradas y salidas
- [x] CODESYS: primer bloque de lógica corriendo en simulación
- [x] Repasar estructura IEC 61131-3: diferencia entre programa, función y bloque de función
- [x] SITRAIN: módulo de arrancadores y protección de motores (1 h)

**Entregable:** descripción funcional y lista de E/S subidas al repositorio.

### Semana 2 · 17–23 de agosto · 11 horas
*Sin reloj de licencia corriendo*

- [x] Máquina de estados del proceso en papel: estados, transiciones, condiciones
- [ ] Implementar la secuencia completa en CODESYS
- [x] Practicar texto estructurado (ST) — es el equivalente al SCL de Siemens
- [ ] Verificar requisitos del equipo y espacio en disco
- [ ] SITRAIN: módulo de SINAMICS, principios de variador (1 h)

**Entregable:** máquina de estados documentada y funcionando en CODESYS.

> Todo lo aprendido aquí se traduce a Siemens en la semana 3. Llegas con la lógica resuelta y solo peleas con el entorno, no con las dos cosas a la vez.

### Semana 3 · 24–30 de agosto · 14 horas
**Instalar TIA Portal V20. Arranca el reloj: vence el 13 de septiembre.**

- [ ] Instalación completa: STEP 7 Professional + WinCC + PLCSIM
- [ ] Configuración de hardware: CPU S7-1500, módulos analógicos y digitales
- [ ] Tabla de tags con nomenclatura consistente
- [ ] Estructura de bloques: OB1, FB de secuencia, FC de alarmas, DB de parámetros
- [ ] Primera descarga a PLCSIM y verificación de comunicación

**Entregable:** proyecto compilando y corriendo en simulación, aunque la lógica esté incompleta.

### Semana 4 · 31 de agosto – 6 de septiembre · 13 horas

- [ ] Secuencia automática completa en ladder
- [ ] Modo manual con enclavamientos de seguridad
- [ ] Transición manual/automático sin arranques inesperados
- [ ] Gestión de alarmas con reconocimiento
- [ ] **Empezar a postular: 20 vacantes esta semana**

**Entregable:** proceso funcionando de principio a fin en PLCSIM.

> Postulas con portafolio a medias a propósito. Los procesos de selección tardan de 3 a 6 semanas, así que cuando te llamen ya vas a tener el HMI.

### Semana 5 · 7–13 de septiembre · 13 horas
**El trial V20 vence el día 13. Cierra todo lo de TIA esta semana.**

- [ ] HMI en WinCC: jerarquía de pantallas
- [ ] Sinóptico del proceso con elementos vinculados a tags
- [ ] Ventana de alarmas y visor de tendencias
- [ ] Pantalla de operación manual
- [ ] **Archivar el proyecto (.zap20) y guardarlo antes de que expire**

**Entregable:** HMI navegable conectado a PLCSIM.

### Semana 6 · 14–20 de septiembre · 14 horas
**Activar trial V21. Vence el 4 de octubre.**

- [ ] Migrar el proyecto a V21 y verificar que todo funcione
- [ ] Animación del sinóptico: bomba girando, nivel subiendo, válvulas cambiando
- [ ] Capa de monitoreo: contador de arranques, horas de operación, corriente
- [ ] Activar la prueba de 30 días de Factory I/O (opcional)

**Entregable:** HMI animado listo para grabar.

### Semana 7 · 21–27 de septiembre · 12 horas

- [ ] Un FB reutilizable escrito en SCL
- [ ] Lazo PID con PID_Compact y escalado de la variable de proceso
- [ ] Sintonía documentada: qué probaste y por qué quedó así
- [ ] Comunicación: Modbus TCP o PROFINET entre dos CPUs

**Entregable:** SCL, PID y comunicación funcionando.

### Semana 8 · 28 de septiembre – 4 de octubre · 12 horas

- [ ] Guion del video: 30 s de contexto, 2 min de funcionamiento, 30 s de decisiones técnicas
- [ ] Grabar con OBS y subir a YouTube
- [ ] Exportar bloques SCL como texto y ladder como PDF
- [ ] README del proyecto completo, incluida la sección de decisiones de diseño
- [ ] Archivar proyecto final y subirlo al repositorio
- [ ] Actualizar LinkedIn con el video

**Entregable:** portafolio publicado.

**Total: 99 horas · promedio 12,4 por semana**

---

## Gestión de licencias

| Recurso | Ventana | Nota |
|---|---|---|
| CODESYS | Permanente | Semanas 1–2 |
| TIA Portal V20 | 24 ago – 13 sep | 21 días desde la instalación |
| TIA Portal V21 | 14 sep – 4 oct | Trial independiente por versión |
| Factory I/O | 30 días | Activar en semana 6, no antes |
| Coursera Plus | Hasta 14 ago | Ya cancelado |

**Si el laboratorio te da licencia SCE, todo esto se cae y ganas dos semanas.** Instalarías TIA Portal desde la semana 1 y el cronograma se adelanta completo.

**Regla:** archiva el proyecto (.zap) al final de cada sesión de trabajo. Un trial que vence con el proyecto adentro y sin respaldo es la peor forma de perder tres semanas.

---

## Frentes paralelos

| Frente | Horas/semana | Cuándo |
|---|---|---|
| TIA Portal + portafolio | 12–14 | Tardes o noches |
| Inglés (Coursera) | 5 | **Mañanas** |
| Tesis | 5–6 | Fines de semana |
| Postulaciones | 2–3 | Desde semana 4 |
| SITRAIN Freemium | 1 | Semanas 1–2 únicamente |

**Total: 25–29 horas semanales.**

Inglés en las mañanas porque no compite cognitivamente con programar. Machine learning sí competiría — por eso arranca en la semana 9, no antes.

Si tienes que recortar algo, recorta SITRAIN. Si vas a apretar, aprieta entre las semanas 3 y 6, que es donde el reloj de licencia hace escasa cada hora.

---

## Después de la semana 8

- Machine learning y Python en Coursera, con ayuda financiera solicitada 3 semanas antes
- Tesis a fondo: adquisición de datos y clasificación de fallas
- Segundo proyecto de portafolio, de naturaleza distinta al primero
- Matrícula profesional COPNIA dentro de los 60 días de graduarte

---

## Checklist de cierre

Al 4 de octubre deberías tener:

- [ ] Video publicado y enlazado en el README
- [ ] Repositorio con documentación, E/S, código exportado y proyecto archivado
- [ ] LinkedIn actualizado con el proyecto
- [ ] Al menos 40 postulaciones enviadas, con registro de cuáles y cuándo
- [ ] Continuidad laboral resuelta, o servicios freelance cubriendo el 50% del ingreso
- [ ] Tema de tesis definido y metodología escrita
- [ ] Curso de inglés terminado o en la unidad 4
- [ ] Colchón de emergencia por encima de $2.500.000

---

## Las tres reglas

1. **Los entregables no se negocian, el calendario sí.** Si algo toma diez semanas en vez de ocho, no pasa nada. Si terminas ocho semanas sin video, sí.
2. **Construir vale más que ver.** Si te sobran horas, programa algo. No veas a alguien programar.
3. **Nada con corchetes se publica.** Antes de cada commit, busca `[` en el editor.
