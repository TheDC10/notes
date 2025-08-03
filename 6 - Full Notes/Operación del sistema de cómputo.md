2025-03-02 19:40

Status: #adult

Tags: [[OS]] [[IO_devices]] [[main_memory]] [[university]]
___
# Operación del sistema de cómputo

- Los dispositivos de E/S y la CPU pueden ejecutarse simultáneamente.
- Cada controlador de dispositivo está a cargo de un tipo de dispositivo en particular.
- Cada controlador de dispositivo tiene un búfer local.
- Cada tipo de controlador de dispositivo tiene un Sistema operativo o device driver para gestionarlo.
- La CPU mueve datos desde/hacia la memoria principal hacia/desde buffers locales.
- La E/S es desde el dispositivo al búfer local del controlador.
- El controlador del dispositivo informa a la CPU que ha finalizado su operación provocando una interrupción.

# Procesador
![[procesador.png]]
Se cuenta desde el índice 0, en la memoria. A cada una de las posiciones en memoria se les denomina palabra. El Contador de programa (PC), guarda la dirección de una instrucción (palabra).
El Registro de instrucciones (IR), guarda la última instrucción obtenida.

>La instrucción es una unidad indivisible de ejecución.

# Ejecución de Instrucción
![[execute.png]]
**Fetch:** Obtener/recuperar una instrucción.
- Este ciclo de instrucción consiste de dos pasos, lectura y ejecución.
- El procesador obtiene la instrucción de memoria a través del PC, que guarda la instrucción en el IR. El contador del programa se incrementa después de cada búsqueda de una instrucción.
- El PC siempre apunta a palabras que corresponden a una instrucción. 

# Registro de Instrucción (IR)
La instrucción recuperada se coloca en el registro de instrucciones. Las instrucciones se caracterizan por ser de los siguientes tipos:
- Memoria del procesador: Transferir datos entre procesador y memoria.
- Procesador- E/S: Datos transferidos hacia o desde un dispositivo periférico.
- Procesamiento de datos: Operación aritmética o lógica en datos.
- Control: Alterar la secuencia de ejecución.

# References
- [[Introducción a SO]]
- [[Organización del sistema de cómputo]]