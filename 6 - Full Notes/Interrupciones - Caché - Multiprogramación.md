2025-03-03 13:29

Status: #adult

Tags: [[OS]] [[main_memory]] [[IO_devices]] [[cache]] [[university]]
___
# Interrupciones - Caché - Multiprogramación
## - ¿Qué es una interrupción?
Las interrupciones alteran la secuencia normal del procesador para gestionar eventos como operaciones de E/S, errores o señales de temporizadores. Al activarse, el procesador pausa la tarea actual, guarda su contexto y ejecuta un manejador específico. Esto permite atender dispositivos más lentos o situaciones críticas sin espera activa, priorizando las interrupciones más urgentes. Finalmente, el procesador recupera el contexto y retoma la ejecución normal.

Clases de interrupciones:
1. **Interrupciones de programas**  
   - **Origen**: Generadas por eventos dentro de la ejecución de un programa.  
   - **Ejemplos**:  
     - *Divisiones por cero*: Cuando el procesador detecta un intento de dividir entre cero.  
     - *Instrucciones inválidas*: Ejecución de códigos de operación no reconocidos.  
     - *Fallas de segmentación*: Acceso a memoria no autorizada (ej: *segmentation fault*).  
     - *Solicitudes deliberadas*: Interrupciones generadas por instrucciones como `int` en ensamblador para llamar a servicios del sistema.  
   - **Características**:  
     - *Síncronas*: Ocurren en un punto específico de la ejecución del programa.  
     - *Manejo*: El sistema operativo (SO) puede terminar el proceso, mostrar un error o redirigir el flujo (ej: excepciones en lenguajes como Java o Python).  

2. **Interrupciones de timers (temporizadores)**  
   - **Origen**: Activadas por un circuito temporizador en el hardware (ej: reloj del sistema).  
   - **Usos principales**:  
     - *Multitarea*: Alternar entre procesos en sistemas de tiempo compartido (*time-sharing*).  
     - *Tareas en tiempo real*: Garantizar respuestas periódicas (ej: sistemas embebidos).  
     - *Planificación del SO*: Para evitar que un proceso monopolice la CPU (*preemptive scheduling*).  
   - **Funcionamiento**:  
     - El temporizador envía una señal periódica (ej: cada milisegundo) para activar una interrupción.  
     - *Asíncronas*: No dependen de la ejecución actual del programa.  
     - El SO utiliza estas interrupciones para recuperar el control de la CPU.  

3. **Interrupciones de E/S (Entrada/Salida)**  
   - **Origen**: Dispositivos periféricos (teclado, disco duro, tarjeta de red, etc.).  
   - **Tipos comunes**:  
     - *Notificación de operación completada*: Ej: un disco duro indica que terminó de leer datos.  
     - *Solicitud de atención*: Ej: el usuario presiona una tecla, y el teclado solicita procesamiento.  
     - *Errores de dispositivo*: Ej: falta de papel en una impresora.  
   - **Manejo**:  
     - El controlador del dispositivo envía una señal al procesador.  
     - El SO ejecuta un *manejador de interrupción* (ISR, *Interrupt Service Routine*) específico para el dispositivo.  
     - *Priorización*: Algunas interrupciones de E/S tienen mayor prioridad (ej: fallas críticas vs. una impresora).  

4. **Interrupciones por fallas de hardware**  
   - **Origen**: Fallos físicos en componentes del sistema.  
   - **Ejemplos críticos**:  
     - *Fallos de alimentación*: Cortes de energía o fluctuaciones graves (manejadas con *UPS*).  
     - *Errores de memoria*: Fallas en RAM detectadas por códigos de corrección de errores (ECC).  
     - *Sobrecalentamiento*: El sensor de temperatura activa una interrupción para apagar el sistema.  
     - *Fallos en la CPU*: Ej: errores de *paridad* en cálculos.  
   - **Respuestas del sistema**:  
     - *Apagado de emergencia*: Para evitar daños mayores.  
     - *Notificación al usuario*: Mensajes de error o registros (*logs*) detallados.  
     - *Recuperación*: En sistemas críticos, se activan componentes redundantes.  
## - Flujo de control de programa
### Sin interrupciones:
![[no_interrups.png]]
En el flujo de control sin interrupciones, el programa de usuario ejecuta llamadas WRITE intercaladas con procesamiento. Las operaciones 1, 2 y 3 no involucran E/S. Cuando se realiza una llamada WRITE, se invoca una rutina de E/S del sistema que sigue estos pasos:

1. **Preparación de la operación de E/S**: Se copian los datos de salida a un buffer y se preparan los parámetros para la orden del dispositivo.
2. **Orden de E/S**: El programa envía la orden al dispositivo y debe esperar a que este complete la operación, posiblemente validando si la E/S ha terminado.
3. **Finalización de la E/S**: Una vez completada la operación, se ejecutan instrucciones para finalizar el proceso, indicando si fue exitoso o falló.

Durante este proceso, el procesador permanece en espera (polling) hasta que el dispositivo termine, lo que puede resultar en una subutilización de la CPU mientras espera. Este enfoque es menos eficiente comparado con el uso de interrupciones, que permitirían al procesador realizar otras tareas mientras espera la finalización de la E/S.
### Con interrupciones:
#### - Espera corta de E/S:
![[espera_corta.png]]
1. **Llamada WRITE**: El programa de usuario invoca la rutina de E/S del sistema.  
2. **Preparación de la E/S**: Se copian datos al buffer, se configuran parámetros y se envía la orden al dispositivo.  
3. **Retorno al programa**: El control regresa al programa de usuario *inmediatamente*, permitiendo que el procesador ejecute otras instrucciones.  
4. **Operación en segundo plano**: El dispositivo de E/S trabaja de forma independiente, accediendo a los datos en memoria para completar la tarea (ej: imprimir).  
5. **Interrupción**: Cuando el dispositivo está listo (ej: para recibir más datos), envía una señal de interrupción al procesador.  
6. **Manejo de la interrupción**: El procesador pausa brevemente la tarea actual, ejecuta el *manejador de interrupciones* para finalizar la E/S (actualizar estados, verificar éxito/error) y retoma el programa de usuario.  

Con este manejo de interrupciones, la CPU no espera inactiva (*polling*), sino que aprovecha el tiempo para procesar otras tareas mientras el dispositivo opera, mejorando la eficiencia global del sistema.
#### - Espera larga de E/S:
![[espera_larga.png]]
1. **Inicio de la operación**:  
   - El programa de usuario ejecuta una llamada `WRITE`, activando una rutina de E/S del sistema.  
   - Se copian datos a un *buffer* y se configura el dispositivo (ej: disco o impresora).  
2. **Retorno inmediato**:  
   - La CPU **no espera** al dispositivo.  
   - El control vuelve al programa de usuario, que sigue ejecutando otras tareas (ej: cálculos).  
3. **E/S en segundo plano**:  
   - El dispositivo trabaja de forma independiente, tardando mucho en completar la operación (*espera larga*).  
   - La CPU aprovecha ese tiempo para procesar otras instrucciones o programas.  
4. **Notificación por interrupción**:  
   - Cuando el dispositivo termina, envía una señal de interrupción a la CPU.  
5. **Manejo de la interrupción**:  
   - La CPU pausa brevemente su tarea actual.  
   - Ejecuta un *manejador de interrupciones* para finalizar la E/S (ej: liberar recursos, notificar éxito/error).  
6. **Continuación normal**:  
   - La CPU retoma la ejecución del programa de usuario desde donde se interrumpió.  

**IMPORTANTE**:  
- **La espera es del dispositivo, no de la CPU**: Aunque la E/S tarde minutos (ej: impresora lenta), la CPU **nunca** queda bloqueada. 
- La rutina de manejo de interrupción se encuentra en la parte baja de la memoria.
## - Procesamiento de Interrupción Simple
![[path_instruction.png]]
1. **Generación de la interrupción**:  
   - Un dispositivo de hardware (ej: controlador de disco, teclado) o evento del sistema detecta una condición que requiere atención (ej: operación de E/S completada, error).  
   - El hardware envía una **señal de interrupción** a la CPU.
2. **Finalización de la instrucción actual**:  
   - La CPU **no interrumpe inmediatamente**. Primero termina de ejecutar la instrucción en curso para mantener la integridad del proceso.
3. **Reconocimiento de la interrupción**:  
   - La CPU emite una **señal de reconocimiento** al dispositivo que generó la interrupción, confirmando que la atenderá.
4. **Guardado del contexto**:  
   - **Salvado del estado del proceso**:  
     - La CPU guarda el **PC (Contador de Programa)** y el **PSW (Palabra de Estado del Programa)** en la *pila de control*.  
     - También guarda registros críticos y el resto del estado del proceso actual (ej: registros de propósito general).  
   - Esto permite retomar la ejecución posteriormente sin pérdida de información.
5. **Carga del manejador de interrupciones**:  
   - La CPU carga un **nuevo valor en el PC**, direccionando la rutina específica del manejador de interrupciones (ISR, *Interrupt Service Routine*).  
   - Este valor se obtiene de una *tabla de interrupciones* predefinida en el sistema operativo.
6. **Ejecución del manejador de interrupciones**:  
   - La CPU ejecuta el código del ISR para atender la interrupción (ej: mover datos del buffer a memoria, manejar un error).  
7. **Restauración del contexto**:  
   - Al terminar el ISR, la CPU recupera el **PC** y **PSW** guardados en la pila de control.  
   - También restaura los registros y el estado del proceso interrumpido.  
8. **Retorno a la ejecución normal**:  
   - La CPU retoma la ejecución del programa original desde la instrucción siguiente a la que estaba ejecutando antes de la interrupción.

**Diagrama simplificado del flujo**:  
```
Hardware → Interrupción → CPU termina instrucción → Guarda PC/PSW/registros → Ejecuta ISR → Restaura PC/PSW/registros → Continúa ejecución
```

**Puntos clave**:  
- La CPU asegura que ninguna operación quede a medias.  
- El proceso original no percibe la interrupción; todo el guardado/restauración es automático.  
- El tiempo de guardado/restauración es mínimo, permitiendo respuestas rápidas a eventos críticos.
## - Cambios en Memoria y Registro
![[stack_pointer.png]]
#### **Componentes clave en la imagen:**  
1. **Pila de Control (Control Stack)**:  
   - Estructura de memoria que almacena temporalmente el estado del programa (ej: registros, PC) durante una interrupción.  
   - Ubicada en el **espacio de memoria del Sistema Operativo (SO)**, asegurando aislamiento y seguridad.  
2. **Stack Pointer (SP)**:  
   - Registro de la CPU que **apunta a la posición actual en la pila de control**.  
   - Indica dónde se guardará o recuperará el próximo dato.  
3. **Interrupt Service Routine (ISR)**:  
   - Rutina del SO que maneja la interrupción. También reside en el espacio del SO.  
4. **Program Counter (PC)**:  
   - Registro que guarda la dirección de la próxima instrucción a ejecutar (N+1 en la imagen).  

---
#### **Flujo durante una interrupción (ej: después de la instrucción en ubicación N):**  
1. **Interrupción ocurre**:  
   - El hardware (ej: dispositivo de E/S) envía una señal de interrupción a la CPU.  
2. **Guardado del estado en la pila de control**:  
   - **Elementos guardados**:  
     - **PC (N+1)**: Dirección de la siguiente instrucción del programa de usuario.  
     - **PSW (Palabra de Estado del Programa)**: Estado actual de la CPU (ej: flags).  
     - **Registros generales**: Valores de registros como AX, BX, etc.  
   - **Mecanismo de inserción**:  
     - Los datos se guardan **desde la parte más alta de la pila hacia direcciones de memoria más bajas**.  
     - El **Stack Pointer (SP)** se **decrementa** para apuntar al nuevo tope de la pila.  
3. **Actualización del Stack Pointer**:  
   - El SP ahora apunta a la **nueva posición** donde se guardó el último elemento (ej: después de decrementar).  
4. **Ejecución del ISR**:  
   - La CPU carga la dirección de la **ISR** (desde la tabla de interrupciones) en el PC.  
   - El ISR maneja la interrupción (ej: leer datos de un dispositivo).  
5. **Restauración del estado**:  
   - Al finalizar el ISR, los datos guardados en la pila (PC, PSW, registros) se recuperan.  
   - El **SP se incrementa** para apuntar a la posición anterior, "liberando" el espacio usado.  
6. **Retorno al programa de usuario**:  
   - El PC recuperado (N+1) permite que la CPU retome la ejecución desde la instrucción siguiente a la interrupción.  
---
#### **Importante:**  
1. **Inserción en la pila**:  
   - Los elementos se agregan **desde la parte más alta** (dirección alta de memoria) y el **tope disminuye** (SP se decrementa). 
2. **Tamaño de registros**:  
   - **SP, PC y Address del IR** tienen el mismo tamaño (ej: 32 o 64 bits), lo que permite almacenarlos y recuperarlos de manera coherente en la pila.
3. **Espacio del Sistema Operativo**:  
   - Tanto la **ISR** como la **pila de control** residen en la memoria del SO, evitando que programas de usuario modifiquen estos recursos críticos.  
---
## - Múltiples interrupciones
![[m_interruptions.png]]
#### **Componentes clave de la imagen:**  
- **User Program**: Programa principal en ejecución.  
- **Interrupt Service Routines (ISR)**:  
  - *Printer ISR*: Maneja interrupciones de la impresora.  
  - *Communication ISR*: Atiende interrupciones de comunicación (ej: red).  
  - *Disk ISR*: Gestiona operaciones de disco (lectura/escritura).  
- **Tiempos**: \( t = 0, 25, 40, 25 \).  

---
#### **1. Interrupciones secuenciales**  
**Flujo**:  
1. **\( t = 0 \)**: El *User Program* comienza su ejecución.  
2. **\( t = 25 \)**: Ocurre una interrupción de la **impresora**.  
   - La CPU guarda el contexto (PC, registros) en la pila de control.  
   - Ejecuta el *Printer ISR* hasta completar la tarea.  
3. **\( t = 40 \)**: Finalizada la interrupción de impresora, ocurre una interrupción de **comunicación**.  
   - La CPU guarda el estado actual del *User Program* y ejecuta el *Communication ISR*.  
4. **\( t = 25 \)**: Tras completar la comunicación, ocurre una interrupción de **disco**.  
   - Se repite el proceso: guardar contexto, ejecutar *Disk ISR*.  
**Características**:  
- Las interrupciones se atienden **una tras otra**, sin solapamiento.  
- El *User Program* se reanuda solo cuando todas las ISR han finalizado.
### **2. Interrupciones anidadas (Nested)**  
**Escenario**:  
- **\( t = 25 \)**: Durante la ejecución del *Printer ISR*, ocurre una interrupción de **comunicación** con **mayor prioridad**.  
- **\( t = 40 \)**: Mientras se maneja la comunicación, ocurre una interrupción de **disco** con **prioridad intermedia**.  
**Flujo**:  
1. **Interrupción de impresora (\( t = 25 \))**:  
   - La CPU guarda el contexto del *User Program* y ejecuta el *Printer ISR*.  
2. **Interrupción de comunicación (\( t = 40 \))**:  
   - Como tiene mayor prioridad, la CPU **interrumpe el *Printer ISR***.  
   - Guarda el contexto de la impresora en la pila y ejecuta el *Communication ISR*.  
3. **Interrupción de disco (\( t = 25 \))**:  
   - Si la prioridad del disco es menor que la comunicación, **no interrumpe**.  
   - El *Communication ISR* termina, restaura su contexto y retoma el *Printer ISR*.  
   - Al finalizar la impresora, se atiende el disco.  
**Características**:  
- Las interrupciones de **alta prioridad** pueden interrumpir a las de **baja prioridad**.  
- Requiere múltiples niveles de guardado/restauración en la **pila de control**.  
#### **3. Importancia de las prioridades**  
- **Jerarquía crítica**:  
  - Ejemplo: *Communication ISR > Disk ISR > Printer ISR*.  
  - Garantiza que eventos urgentes (ej: fallos de red) se atiendan primero.  
- **Evita bloqueos**:  
  - Sin prioridades, una ISR lenta (ej: impresora) retrasaría tareas críticas (ej: disco).  
- **Gestión eficiente de recursos**:  
  - La CPU no desperdicia ciclos en esperas innecesarias.  
#### **Rol de la pila de control y el Stack Pointer (SP):**  
- **Guardado anidado**: Cada interrupción guarda su contexto en la pila, decrementando el SP.  
- **Restauración inversa**: Al finalizar una ISR, el SP se incrementa para recuperar el contexto anterior. 
## - Ciclo de manejo de interrupción de E/S

![[cycle_interruption.png]]
#### **Componentes clave:**  
1. **CPU**: Unidad Central de Procesamiento, encargada de ejecutar instrucciones.  
2. **Device Driver**: Software que controla un dispositivo de hardware (ej: impresora, disco).  
3. **I/O Controller**: Circuito que gestiona la comunicación entre la CPU y los dispositivos de E/S.  
4. **Interrupt Handler (Manejador de Interrupciones)**: Rutina del sistema operativo que atiende las interrupciones.  
#### **Flujo paso a paso:**  
**1. Estado inicial de la CPU:**  
   - La CPU está ejecutando instrucciones del programa de usuario o del sistema operativo.  
**2. Inicio de la operación de E/S:**  
   - El *device driver* envía una solicitud al **I/O controller** (ej: leer datos de un disco).  
   - El I/O controller inicia la operación física con el dispositivo (ej: disco busca los datos).  
**3. Verificación de interrupciones**:
   - Entre la ejecución de cada instrucción, la CPU **verifica si hay interrupciones pendientes**.  
   - Esto evita que una operación larga bloquee la atención a eventos críticos.  
**4. Generación de la interrupción**:  
   - Cuando el dispositivo completa la operación (ej: datos listos, error, o finalización de escritura), el **I/O controller** envía una **señal de interrupción** a la CPU.  
**5. Transferencia de control**:  
   - La CPU **detiene temporalmente** su ejecución actual.  
   - Guarda el contexto (PC, registros) en la **pila de control**.  
   - Transfiere el control al *interrupt handler* correspondiente (ej: rutina para manejar datos del disco).  
**6. Procesamiento de la interrupción**:
   - El *interrupt handler* ejecuta las acciones necesarias (ej: copiar datos del buffer a memoria).  
   - Si hay errores, notifica al sistema operativo o usuario.  
   - Al terminar, restaura el contexto guardado y retorna el control a la CPU.  
**7. Continuación de la tarea**:  
   - La CPU retoma la ejecución del programa desde donde se interrumpió, como si nada hubiera pasado. 
## - Ejercicio de clase
![[eje_clase.jpg]]
## - Multiprogramación
- El procesador tiene más de un programa para ejecutar.
- La secuencia en que se ejecutan los programas depende de su prioridad relativa y de si están esperando E/S.
- Después de que un controlador de interrupciones termina, es posible que el control no regrese al programa que se estaba ejecutando en el momento de la interrupción.
## - Jerarquía de Memoria
![[piramide_memoria.png]]
#### **1. *"Tiempo de acceso más rápido, mayor costo por bit"***  
**Significado**:  
Los componentes de memoria que ofrecen un **acceso más rápido** a los datos (ej: registros de la CPU, caché L1) son **más caros por bit almacenado**.  
**Razón**:  
- Tecnologías avanzadas:  
  - Memorias ultrarrápidas como SRAM (usada en cachés) requieren más transistores y circuitos complejos, aumentando el costo.  
- Escala de fabricación:  
  - Producir memorias pequeñas y veloces es técnicamente complejo y costoso.  
#### **2. *"Mayor capacidad, menor costo por bit"***  
**Significado**:  
Las memorias con **mayor capacidad de almacenamiento** (ej: discos duros, SSDs) tienen un **costo más bajo por bit**. 
**Razón**:  
- Economías de escala:  
  - Fabricar memorias masivas (ej: 1 TB) reduce el costo unitario por bit.  
- Tecnologías sencillas:  
  - Memorias como HDDs usan componentes mecánicos o celdas NAND (en SSDs), más baratas de producir en masa.  
#### **3. *"Mayor capacidad, velocidad de acceso más lenta"***  
**Significado**:  
Los dispositivos con **mayor capacidad** (ej: discos duros, cintas magnéticas) suelen tener **velocidades de acceso más lentas**.  
**Razón**:  
- Limitaciones físicas:  
  - En discos duros, el cabezal debe moverse para leer datos, generando latencia mecánica.  
- Complejidad de gestión:  
  - Buscar datos en un espacio enorme (ej: 1 TB) toma más tiempo que en memorias pequeñas (ej: caché de 4 MB). 
#### **Relación con la Jerarquía de Memoria:**  
La jerarquía de memoria organiza los componentes según estos principios:

| **Nivel**         | **Ejemplo**         | **Tiempo de Acceso** | **Costo por bit** | **Capacidad**     |  
|--------------------|---------------------|-----------------------|-------------------|-------------------|  
| **Registros**      | Registros de CPU    | 0.3 ns               | Muy alto          | KBs               |  
| **Caché (L1/L2)**  | SRAM                | 1-10 ns              | Alto              | MBs               |  
| **RAM**            | DDR4/DDR5           | 50-100 ns            | Moderado          | GBs               |  
| **Almacenamiento** | SSD/HDD             | 50 μs - 10 ms        | Bajo              | TBs               |  
Esto explica la relación inversa entre velocidad, costo y capacidad en la jerarquía de memoria:  
- **Velocidad vs. Costo**: Pagas más por acceder rápido.  
- **Capacidad vs. Velocidad**: Más espacio implica más lentitud.  
- **Capacidad vs. Costo**: A mayor capacidad, menor costo por unidad.
## - Memoria secundaria
La memoria secundaria es un tipo de almacenamiento no volátil y auxiliar que se utiliza para guardar de manera permanente archivos de programas, datos y otros contenidos, incluso cuando el dispositivo está apagado. A diferencia de la memoria primaria, su función principal es proporcionar capacidad de almacenamiento persistente y accesible a largo plazo para aplicaciones y usuarios.
## - Caché de disco
El caché de disco es una porción de la memoria principal que actúa como búfer para almacenar temporalmente datos del disco, agrupando las escrituras y permitiendo su recuperación rápida desde este espacio en lugar del disco físico. Algunos datos almacenados aquí pueden ser reutilizados, lo que optimiza el rendimiento al reducir el acceso lento al disco y mejorar la eficiencia en operaciones de lectura/escritura.
## - Memoria Caché
La memoria caché es un componente diseñado para aumentar la velocidad de acceso a los datos, mitigando la diferencia entre la rápida velocidad del procesador y la menor velocidad de la memoria principal. Funciona explotando el principio de cercanías de referencias, almacenando copias temporales de datos usados frecuentemente. Al ser transparente e invisible para el sistema operativo, opera de manera autónoma, permitiendo recuperar información críticas de forma inmediata y reduciendo los tiempos de espera en operaciones repetitivas.
![[cache.png]]
La caché almacena copias de una porción de la memoria principal para acelerar el acceso a datos frecuentemente utilizados. Cuando el procesador requiere información, primero verifica este espacio; si no la encuentra, el bloque de memoria relevante se transfiere a la caché y luego se envía al procesador. Este mecanismo reduce la necesidad de acceder directamente a la memoria principal, optimizando la eficiencia y minimizando los tiempos de espera.
#### - Ejemplo:
Para este caso, la memoria tiene un tamaño de 32 palabras y queremos hacer bloques de 4 palabras:
$\text{tamMP} = 32 \text{ pal} = 2^5 \text{ pal}, n=5$
$n = \text{número de bits para establecer una dirección de MP}$  
$\text{tamBlq} = 4 \text{ pal} = 2^2 \text{ pal}, m=2$
$m = \text{número de bits para establecer desplazamiento dentro de un bloque}$
$\text{blqs en MP} = \frac{32}{4} = 8 \text{ blqs} = 2^3 \text{ blqs, b=3}$
$b = \text{número de bits para establecer una dirección de bloque}$

| Bloques | MP  | Address$_{(10}$ | Address$_{(2}$ |
| ------- | --- | --------------- | -------------- |
| B0      |     | 0               | 000 \| 00      |
| B0      |     | 1               | 000 \| 01      |
| B0      |     | 2               | 000 \| 10      |
| B0      |     | 3               | 000 \| 11      |
| B1      |     | 4               | 001 \| 00      |
| B1      |     | 5               | 001 \| 01      |
| B1      |     | 6               | 001 \| 10      |
| B1      |     | 7               | 001 \| 11      |
| B2      |     | 8               | 010 \| 00      |
| B2      |     | 9               | 010 \| 01      |
| B2      |     | 10              | 010 \| 10      |
| B2      |     | 11              | 010 \| 11      |
| B3      |     | 12              | 011 \| 00      |
| B3      |     | 13              | 011 \| 01      |
| B3      |     | 14              | 011 \| 10      |
| B3      |     | 15              | 011 \| 11      |
|         | ___ | ___             | ___            |
| B7      |     | 28              | 111 \| 00      |
| B7      |     | 29              | 111 \| 01      |
| B7      |     | 30              | 111 \| 10      |
| B7      |     | 31              | 111 \| 11      |
En esta tabla que representa la memoria principal (MP), se ve que las direcciones en memoria se conforman de 5 bits ($n$), donde los primeros 3 bits ($b$) corresponden a la dirección del bloque y los 2 bits restantes ($m$) corresponden al desplazamiento dentro del bloque. (En la tabla se separa con una barra para que sean más evidente las direcciones).

|          | MP      | despl. en un bloque | bloque              |
| -------- | ------- | ------------------- | ------------------- |
| bits     | $n$     | $m$                 | $b = n-m$           |
| palabras | $2^n$   | $2^m$               | $2^b=2^{n-m}$       |
| max_addr | $2^n-1$ | $2^m-1$             | $2^b-1 = 2^{n-m}-1$ |
# References
- [[Organización del sistema de cómputo]]
- [[Operación del sistema de cómputo]]
- [[Máquina hipotética]]