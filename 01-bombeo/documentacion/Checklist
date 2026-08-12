# Checklist de entregables

**Estado al 12 de agosto de 2026**
Archivo personal — no subir al repositorio.

---

## Fase 1 · Diseño y prototipo — COMPLETA

### Documentación
- [x] Descripción funcional (revisión 2)
- [x] Diagrama P&ID
- [x] Lista de entradas y salidas, fase 1
- [x] Máquina de estados con tabla de transiciones
- [x] Diez decisiones de diseño documentadas con su razonamiento

### Lógica en CODESYS
- [x] `FB_Bomba` como bloque de función instanciable
- [x] Máquina de seis estados operativos más FALLA
- [x] Temporizadores de rearranque, arranque, aceleración y parada
- [x] Enclavamientos evaluados antes del bloque de estados
- [x] Registro de primera causa (`iCausaFalla`)
- [x] Flanco de subida en marcha y en reconocimiento
- [x] Distinción AL-05 (eléctrico) / AL-09 (mecánico)
- [x] AL-10, parada no completada en tiempo
- [x] Modelo de motor para simulación
- [x] Ciclo completo probado, incluidas las cuatro rutas de falla

### Entorno
- [x] Repositorio GitHub creado y publicado
- [x] CODESYS instalado y funcionando
- [x] TIA Portal V20 TRIAL descargado
- [x] Equipo verificado (12 GB RAM, 9,94 utilizables)
- [x] Cuenta SITRAIN Access con categorías elegidas
- [x] Coursera configurado, curso de inglés elegido, suscripción cancelada

---

## Fase 2 · Cierre antes de TIA Portal — EN CURSO

Todo esto debe estar listo **antes** de instalar. Cada hora aquí es una hora
que no se gasta con el trial corriendo.

### Repositorio
- [ ] Subir `descripcion-funcional.md` revisión 2
- [ ] Subir el `.project` de CODESYS a `01-bombeo/codigo/codesys/`
- [ ] Actualizar README: quitar las transiciones a falla de trabajo pendiente
- [ ] Actualizar el estado del proyecto en el README
- [ ] Corregir el diagrama de draw.io: dibujar las flechas al estado 9 y
      eliminar la nota de pendiente
- [ ] Corregir el P&ID: eliminar los globos duplicados de FT-01 y LT-01

### Código
- [ ] Devolver `tTiempoRearranque` a `T#60S` (está en valor de prueba)
- [ ] Verificar el resto de constantes contra la descripción funcional
- [ ] Descomentar todo lo que se comentó para provocar fallas
- [ ] Liberar forzados y hacer una pasada completa del ciclo

### Decisiones de hardware
- [ ] Referencia exacta de CPU (sugerencia: 1511-1 PN)
- [ ] Módulos de señal: DI, DQ, AI, AQ
- [ ] Panel HMI (sugerencia: KTP700 Basic)
- [ ] Convención de nombres de tags para TIA Portal

### Licencia
- [ ] Descargar V21 (dos archivos: 8,1 GB + 2,5 GB)
- [ ] Decidir fecha de instalación

**Nota de licencia:** el manual oficial dice que el trial se activa una sola vez
por equipo y dura 21 días. La ventana real es de tres semanas desde la
instalación. La V21 queda como experimento al vencer la V20: si ofrece activar
trial, son 21 días extra; si no, no se pierde nada.

---

## Fase 3 · TIA Portal — 21 días desde la instalación

### Semana 1
- [ ] Instalar STEP 7 Professional + WinCC + PLCSIM
- [ ] Configurar CPU y módulos
- [ ] Tabla de tags completa antes de escribir lógica
- [ ] Estructura de bloques: OB, FB, FC, DB
- [ ] Traducir `FB_Bomba` a SCL
- [ ] Descarga a PLCSIM y verificación del ciclo completo

### Semana 2
- [ ] Jerarquía de pantallas en WinCC
- [ ] Sinóptico del proceso con tags vinculados
- [ ] Ventana de alarmas conectada a la palabra de bits
- [ ] Pantalla de operación manual
- [ ] Visor de tendencias de nivel

### Semana 3
- [ ] Animación del sinóptico
- [ ] Capa de monitoreo: horas, arranques, corriente
- [ ] Guion del video: 30 s de contexto, 2 min de funcionamiento,
      30 s de decisiones técnicas
- [ ] Grabar con OBS y subir a YouTube
- [ ] Exportar bloques SCL como texto
- [ ] Archivar el proyecto (`.zap20`) y subirlo al repositorio
- [ ] README del proyecto completo con el video enlazado

**Queda fuera por la ventana de 21 días:** regulación PID, comunicación con el
variador y sintonía. Van a trabajo pendiente del README.

**Regla del trial:** archivar el proyecto al final de cada sesión.

---

## Fase 4 · Portafolio y empleo — EN PARALELO

### Presencia
- [ ] LinkedIn actualizado con el proyecto y el video
- [ ] Hoja de vida con el enlace al repositorio
- [ ] Formación en SITRAIN redactada sin inflar
      ("formación autodirigida en plataforma oficial Siemens", nunca
      "certificación Siemens")

### Postulaciones
- [ ] Primeras 20 postulaciones — **empezar con el portafolio a medias**
- [ ] Registro de a qué empresas y cuándo
- [ ] 40 postulaciones acumuladas al cierre de la fase 3
- [ ] Preparar respuestas a las cinco decisiones de diseño principales

Los procesos tardan de 3 a 6 semanas entre postulación y entrevista técnica.
Postular con portafolio parcial es mejor que postular tarde con portafolio
completo.

### Pasantía y tesis
- [ ] Conversación con el jefe: tesis en la empresa y posibilidad de vinculación
- [ ] Fecha exacta de terminación por escrito
- [ ] Certificación laboral con funciones y fechas solicitada
- [ ] Tema de tesis definido
- [ ] Metodología escrita

### Formación continua
- [ ] Curso de inglés: unidades 1 a 5
- [ ] SITRAIN: Low-voltage controls, SINAMICS Converter, SIMATIC Automation

---

## Fase 5 · Después del video

- [ ] Segundo proyecto de portafolio, de naturaleza distinta al primero
- [ ] Python y análisis de datos en Coursera, con ayuda financiera solicitada
      tres semanas antes
- [ ] Tesis a fondo: adquisición de datos y clasificación de fallas
- [ ] Matrícula profesional COPNIA dentro de los 60 días de graduarse

---

## Track financiero — EN PARALELO A TODO

Esta es la razón de fondo del resto del plan: reducir la dependencia de una sola
entrada de dinero antes de que termine la pasantía.

### Defensiva — antes de que termine la pasantía

Lo primero no es invertir, es no quedar expuesto cuando se acabe el ingreso.

- [ ] Medir los gastos reales durante 30 días (sin esto, el colchón es una
      adivinanza)
- [ ] Activar la exención de 4x1000 en una sola cuenta
- [ ] Mover el capital disponible a cuenta de alto rendimiento con **liquidez
      diaria**
- [ ] Ahorro automático cada quincena, el mismo día que entra el ingreso
- [ ] Cero deuda de consumo nueva
- [ ] Colchón equivalente a 3 meses de gastos antes de la fecha de terminación

**No bloquear dinero a plazo fijo** mientras el ingreso tenga fecha de
vencimiento. Los CDT entran cuando haya ingreso estable otra vez, no antes.

### Los siete flujos, en orden de implementación

| # | Flujo | Estado |
|---|---|---|
| 1 | Continuidad laboral o empleo especializado | En curso — depende de la conversación pendiente |
| 2 | Documentación técnica y CAD eléctrico | No iniciado |
| 3 | Programación PLC/HMI para PYMES | Depende del portafolio |
| 4 | SCADA, dashboards y datos en USD | Mes 7 en adelante |
| 5 | Productos digitales técnicos y afiliados | Mes 9 en adelante |
| 6 | Alquiler de instrumentos | Año 2, financiado con caja |
| 7 | Renta fija, después dividendos | Cuando el ingreso sea estable |

### Flujo 2 — el de arranque más rápido

Costo cero: las licencias educativas de CAD son gratuitas y no requiere matrícula
profesional. Es la caja que sostiene todo lo demás.

- [ ] Tres piezas de portafolio CAD (planos de tablero, unifilares de control)
- [ ] Perfil en Workana o Upwork
- [ ] Primeros 10 contactos a talleres e integradores
- [ ] Definir tarifa por hora y subirla cada vez que se rechace un cliente

### Formalización, cuando haya ingresos por servicios

- [ ] Cuenta bancaria separada para el negocio
- [ ] RUT actualizado
- [ ] Evaluar Régimen Simple
- [ ] Aportes a seguridad social como independiente

### Resistencia — revisar cada trimestre

- [ ] Ninguna fuente supera el 55 % del ingreso total
- [ ] Ningún cliente supera el 35 % de los ingresos por servicios
- [ ] Al menos un flujo en moneda distinta al peso
- [ ] Al menos un flujo que no dependa de horas trabajadas
- [ ] EPS activa y aportes al día
- [ ] ARL activa siempre que haya trabajo de campo
- [ ] Contratos escritos con todos los clientes, anticipo del 50 %

### La conexión que no es obvia

El portafolio de automatización **es** el flujo 3. No son dos proyectos
paralelos: el video y el repositorio son lo que permite cobrar por programar un
PLC en vez de buscar cualquier trabajo.

Y el orden importa. Con el capital disponible actual, la renta fija produce unas
pocas decenas de miles de pesos al mes. Las mismas horas dedicadas a construir
capacidad técnica producen dos órdenes de magnitud más. Por eso el año 1 convierte
horas en ingresos, y solo después convierte ingresos en capital que trabaje solo.

---

## Lo que falta y no es técnico

Dos cosas llevan semanas pendientes y no dependen de ninguna herramienta:

1. **La conversación sobre tesis y vinculación.** Todo el plan de empleo depende
   de saber si hay continuidad o no.
2. **Las primeras postulaciones.** Con el portafolio al 60 % ya hay material
   suficiente para empezar.
