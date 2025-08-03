2025-02-24 14:58

Status: #adult

Tags: [[OS]] [[introduction]] [[university]]
___
# Introducción a SO
#### ¿Qué es el SO?
Es un programa que actúa como intermediario entre el hardware y el usuario del computador.
Un Sistema Operativo: Ejecuta programas de usuario, soluciona problemas de forma más fácil, hace el sistema de cómputo más conveniente para el usuario y utiliza el hardware del computador de una forma eficiente.

#### ¿Qué hace el SO?
| Para el usuario                                      | Para el sistema                                                                              |
| ---------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| No se preocupa por la utilización de recursos        | El SO es un programa de control y asignador de recursos                                      |
| Interfaces de usuario                                | Decide cuando hay conflictos por requerimientos para un uso eficiente de los recursos        |
| Ejecutar principalmente sin intervención del usuario | Controla la ejecución de programas para prevenir errores y el uso inapropiado del computador |
|                                                      | Proporciona un conjunto de servicios a los usuarios del sistema                              |
#### Componentes del computador - Visión Abstracta
![[comps_os.png]]
#### Definición
Pese a que no es una definición universalmente aceptada, decir que: “Todo lo que un proveedor envía cuando el cliente ordena es un sistema operativo" es una buena aproximación aunque varíe ampliamente.

"El único programa que se ejecuta en todo momento en la computadora" es el núcleo (kernel). Todo lo demás es un programa del sistema o un programa de aplicación:
- Programa del sistema: viene con el sistema operativo, pero no forma parte del núcleo.
- Programa de aplicación: todos los programas no asociados con el sistema operativo.

Los sistemas operativos actuales para uso general y computación móvil también incluyen middleware: un conjunto de frameworks que brindan servicios adicionales a los desarrolladores de aplicaciones, como bases de datos, multimedia, gráficos.
___
## Nota
- **Deadlock (Abrazo mortal):** Estado en el que dos o más procesos en un sistema quedan bloqueados permanentemente, cada uno esperando un recuso que otro proceso posee, sin que ninguno pueda avanzar. Es un problema crítico en sistemas operativos debido a la gestión concurrente de recursos.