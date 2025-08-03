2025-02-24 22:40

Status: #adult 

Tags: [[OS]] [[IO_devices]] [[main_memory]] [[university]]
___
# Organización del sistema de cómputo

En el Sistema de Cómputo, mediante una o más CPUs, los controladores de dispositivos se conectan a través de un bus común que proporciona acceso a la memoria compartida. Esta ejecución concurrente de CPU y dispositivos compiten por ciclos de memoria.
![[devices_os.png]]
Dispositivos como el disco y el teclado tienen controladoras que se comunican con la CPU hechas para manejar ese único dispositivo.

```python
# IR, Instruction Registry
print(IR.size == Palabra.size) # True
"""
La instrucción se encuentra en el IR, 
el IR está en la CPU, la CPU ejecuta.
"""
# Fetch (lectura)
PC = 20 # Program Counter
IR = MP[PC]
PC = PC + 1
```

>Una instrucción es indivisible (Atómica).

>El IR tiene un código de operación y una dirección de memoria.

**Nota:** Si el PC mide 16 bits, la dirección de los datos en una instrucción también mide 16 bits.
### Elementos Básicos
1. Procesador
2. Memoria principal
   - Volátil.
   - Referenciada como memoria real o memoria primaria.
3. Módulo de E/S
   - Dispositivos de memoria secundaria.
   - Equipo de comunicaciones.
   - Terminales.
4. Sistema de bus
   - Comunicación entre procesadores, memoria y módulos de E/S

# References

- [[Introducción a SO]]