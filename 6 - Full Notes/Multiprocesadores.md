___
2025-05-16 14:45

Status: #young 

Tags: [[PC2]] [[uninorte]] [[pipeline]] 
___
# Multiprocesadores
- Es el ideal de diseño de computadores
- El software es pieza clave para lograr que este ideal se vuelva realidad
  
### Dos paradigmas
Desde un punto de vista del usuario es un trabajo paralelo y desde un punto de vista del sistema es secuencial.
- Paralelismo a nivel de trabajos
- Paralelismo a nivel de procesamiento
### Conceptos importantes
- Multiprocesadores
- Multicores

### Retos programas en paralelo
¿Por qué es más complejo? Cuando tenemos un algoritmo en paralelo, se toman todas las tareas y se paralelizan.
- Sincronización

# MPI
## Intro
````docker
docker run -d -it --name mpicont -v ....
````
- Protocolo de comunicación para implementar algoritmos en paralelo.
- Puede ser usada en unca computadora o en un cluster.
- Implementación principal en C++
- Implementaciones alternas en otros lenguajes.
## Funcionalidad
- Distribución de trabajo: Entre entidades llamadas nodos, identificados por un rango.
- Primitivas: Sincronización y paso de mensajes.

El trabajo en paralelo es sencillo de usar pero las decisiones que se toman en la arquitectura o problema que se tenga, lo puede hacer más complejo.

````python
from mpi4py import MPI
comm = MPI.COMM_WORLD
rank = comm.Get_rank()
if rank == 0:
	data = [1,2,3]
	comm.send(data, dest=1)
elif rank == 1:
	data = comm.recv(source=0)
	print(data)
````
# References