# ST0263 Tópicos Especiales en Telemática, 2024-2

# Estudiante(s): 
Miguel Angel Calvache Giraldo - macalvachg@eafit.edu.co
Miguel Angel Martinez Garcia - mamartin11@eafit.edu.co

# Profesor:

Edwin Nelson Montoya - emontoya@eafit.brightspace.com

# Arquitectura P2P y Comunicación entre procesos mediante API REST, RPC y MOM


# 1. breve descripción de la actividad

Diseñar e implementar un sistema P2P descentralizado y distribuido basado en la arquitectura DHT/Chord tipo anillo. Cada nodo (peer) del sistema debe soportar un conjunto de microservicios que faciliten la compartición de archivos.

### Requisitos del Sistema:

* Estructura de Red: La red P2P es estructurada, utilizando DHT/Chord. 
* Nodos/Peers: Cada nodo incluye un módulo servidor (PServidor) y un módulo cliente (PCliente). El servidor debe soportar concurrencia para permitir múltiples conexiones simultáneas.
* Acceso al Servicio: Todos los peer entran por un peer de arranque (bootstrap peer) para unirse a la red.
* Mantenimiento de la Red y Localización de Recursos: El sistema debe gestionar la red P2P y localizar recursos, pero no realizará transferencias de archivos reales. Sin embargo, implementarservicios ECO o dummies para simular la descarga y carga de archivos.
* Comunicación entre Procesos: Se utiliza middleware como API REST, gRPC y MOM para implementar la comunicación RPC.
* Recursos Compartidos: Cada peer compartirá un índice de archivos disponibles en un directorio configurable durante el inicio del microservicio.
* Archivo de Configuración: Cada peer tiene un archivo de configuración que definirá parámetros como la IP, puerto de escucha, directorio de archivos, y la URL de un peer de bootstrap inicial.

## 1.1. Que aspectos cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)

* Diseño: Se logró diseñar e implementar una red P2P que soporta un sistema de compartición de archivos distribuidos.
* Desarrollo: Se desarrollo el protocolo Chord DHT.
* Implementación: Se logró implementar una red P2P estructurada basada en Chord / DHT. Esta red con tipología de anillo, nos permite manejar de manera óptima el sistema de compartición de archivos distribuido.
* Creación de la red P2P: El punto de acceso a la red, es cualquier otro peer que se encuentre conectado en esta.
* Microservicios: Se implementaron los microservicios de carga y descarga de archivos, así como también la conexión y desconexión de peers en la red.
* Concurrencia: Se da un correcto manejo a la concurrencia de procesos por medio de la implementación de hilos.
* Despliegue: La red P2P fue desplegada con éxito en una instancia EC2 de AWS.


## 1.2. Que aspectos NO cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)

* gRPC: No se logró realizar la transferencia de archivos por medio del middlewear gRPC debido a que la conexión de transferencia de archivos era inestable. Lo que nos representó un gran desafío al momento de implementar el proyecto.

# 2. información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas.

## Componentes.
* Nodo: Cada instancia / peer de la red es un nodo que puede unirese, dejar, subir o descargar archivos en la red.
* Finger Table: Es una tabla de enrutamiento que permite a cada nodo optiizar la búsqueda de otros nodos de la red, generando “shortcuts” entre la red.
* Red: Es el conjunto de nodos que se encuentran interconectados a través de sus direcciones IP y Puertos.
* API REST: Manejo de los servicios de nodo a través de enpoints HTTP usando Flask.

## Arquitectura de la red.
* Tipología: La red se encuentra estructurada en forma de anillo. Se consta de un conjunto de nodos distribuidos entre sí que se comunican para la transferencia de archivos dentro de la red.
* Comunicación: La comunicación a través de la red distribuida se da por medio de rutas API REST. 
* Enrutamiento: Para la búsqueda de información dentro de la red, se establece una tabla de enrutamiento o finger table, la cual permite mejorar la complejidad del algoritmo, pasando de O(N) a O(logn). La finger table establece shortcuts a través de la red, para facilitar la comunicación entre nodos.
* Apuntadores: Cada peer, tiene una referencia directa al nodo sucesor y predecesor en la red, permitiendo una mejor navegación a través de la red.

## Patrones de diseño.
* P2P: A través de chord, se establece una red distribuida en donde no existe un punto central de control. 
* Consistent Hashing: Es el mecanismo utilizado por Chord que permite asignar claves únicas a los nodos dentro de la red.

## Mejores prácticas.
* Estabilización de la red: Por medio de una función ping, los nodos envían un ping de manera periódica a su sucesor, con el fin de detectar cambios en la red. En el caso de que su sucesor ya no se encuentre activo, este realiza un protocolo de estabilización de la red en donde se actualizan su sucesor correspondiente, así como también la actualización de las finger table de todos los nodos en la red.
* Finger table: Esta tabla de enrutamiento contiene referencias a otros nodos en la red, con cada entrada apuntando a nodos progresivamente más distantes. Esto permite a un nodo saltar grandes secciones del anillo, reduciendo la cantidad de saltos necesarios para localizar el nodo responsable de una clave. Si desea conocer el algoritmo de esta, remítase a la bibliografía adjunta.
* Concurrencia: La red P2P implementada maneja la concurrencia de sus procesos a través de hilos en el código. Lo que permite que varios procesos se ejecuten de manera simultánea sin verse afectados entre sí.

![Arquitectura](Arquitectura-dht-chord-ring.png)

# 3. Descripción del ambiente de desarrollo y técnico: lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.

## Lenguaje de programación.
* El proyecto fue realizado en el lenguaje de programación Python.

## Librerías.
* socket: Creación y gestión de conexiones de red a través de sockets que emplean el protocolo TCP/IP
* threading: Implementación y manejo de hilos (concurrencia).
* pickle: Serializar y deserializar objetos, con el fin de enviarlos a través de la red.
* json: Manejo de datos en formato JSON.
* time: Manejo de intervalos de tiempo y pausas.
* hashlib: Generación de valores hash para crear identificadores a través del algoritmo SHA-1.
* os: Interacciones con el sistema operativo para el manejo de archivos.
* collections: Implementación de diccionarios ordenados.
* flask: Creación, gestión e implementación del middlewear API REST.
* request: manejo de solicitudes HTTP.
* jsonify: Conversión de datos a formato JSON.

## Paquetes.
* Remítase al archivo requirements.txt en donde encontrará todos los paquetes y versiones necesarias.


## Compilación y ejecución.
* Ejecute el comando: pip install -r requirements.txt
* Cambie los parámetros del archivo “config.json” por los de su instancia EC2 de AWS
* Ejecute el comando: python nodo.py
* NOTA: Asegúrese de que se encuentre en el directorio correcto de cada nodo para ejecutar el archivo nodo.py
* Una vez abra la dirección web en el navegador, consulte las siguientes rutas si lo desea:
- Conexión a la red: …/joinNetwork/<ip flask del nodo a conectar>/<puerto flask del nodo a conectar>
- Desconexión de la red: …/leaveNetwork
- Carga de archivo: …/uploadFile/<nombre del archivo>
- Descarga de archivo: …/downloadFile/<nombre del archivo>
- Consultar Finger Table: …/printFTable
- Consultar información del nodo: …/printMyInfo
- Consultar archivos del nodo: …/printMyFiles


## Detalles técnicos y de desarrollo.
* El proyecto implementa un sistema distribuido basado en el protocolo Chord utilizando Python. La clase Node define un nodo de la red con funcionalidades clave como la gestión de conexiones, transferencia de archivos y mantenimiento de la tabla de dedos.
* Componentes principales:
* Configuración: Carga de parámetros desde un archivo config.json (IP, puertos, directorio de archivos).
* Hashing: Utiliza SHA-1 para generar identificadores de nodos y archivos.
* Flask: Expone una API REST para unirse a la red, dejar la red, subir y descargar archivos.
* Conexiones: Usa sockets para la comunicación entre nodos y para la transferencia de archivos.
* Finger Table: Mantiene y actualiza una tabla de dedos para facilitar la búsqueda de nodos y archivos en la red.
* Mantenimiento de Red: Implementa métodos para manejar unirse, dejar la red, y actualizar la tabla de dedos. También gestiona la conexión y desconexión de nodos.
* El nodo puede aceptar conexiones, manejar solicitudes de archivos y mantener la integridad de la red actualizando las tablas de dedos de otros nodos. Además, emplea hilos para manejar conexiones concurrentes y usa Flask para permitir operaciones HTTP.


## Descripción y como se configura los parámetros del proyecto (ej: ip, puertos, conexión a bases de datos, variables de ambiente, parámetros, etc)

* El archivo de configuración config.json establece los valores para los parámetros utilizados en el proyecto. Cada nodo cuenta con dos direcciones IP y 2 puertos. 
* “ip”: Dirección IP privada de la instancia EC2. Utilizada para la comunicación interna entre nodos
* “port”:  Puerto que utilizará el socket del nodo para la comunicación.
* “ip_flask”: Dirección IP para la ejecución de Flask.
* “port_flask”: Puerto utilizado para la ejecución de Flask.


## Opcional - detalles de la organización del código por carpetas o descripción de algún archivo. (ESTRUCTURA DE DIRECTORIOS Y ARCHIVOS IMPORTANTE DEL PROYECTO, comando 'tree' de linux)
* La estructura de carpetas se encuentra dividida en cada uno de los nodos de la red (8 en este caso). Dentro de cada carpeta encontrará los archivos "config.json" y "nodo.py" además de los archivos de texto que correspondan a cada nodo. 


# 4. Descripción del ambiente de EJECUCIÓN (en producción) lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.
* Para la ejecución del proyecto, este fue desplegado en una instancia EC2 de AWS. Es en esta instancia en donde se compila y se ejecuta el código del proyecto.
* Para el manejo de conexiones, se estableció un grupo de seguridad que permite gestionar tanto el tráfico saliente como el tráfico entrante a la instancia. Esto con el fin de manejar las peticiones realizadas entre nodos a través de API REST.
* Se definen reglas de entrada y de salida para gestionar el rango de puertos utilizados por los nodos. 
* Se utiliza la IP privada de la instancia EC2 y el primer rango de puertos para la comunicación privada entre los nodos de la red P2P.
* Se utiliza la IP pública de la instancia EC2 y el segundo rango de puertos para la comunicación de las peticiones por medio de API REST.

# IP o nombres de dominio en nube o en la máquina servidor.
* IP pública: 44.202.20.239
* IP privada: 172.31.82.121

## descripción y como se configura los parámetros del proyecto (ej: ip, puertos, conexión a bases de datos, variables de ambiente, parámetros, etc)

* Remítase al numeral anterior.

## como se lanza el servidor.
* Ingrese a AWS y cree una nueva instancia EC2 t2.micro.
* Descargue el par declaves por medio del archivo .pem generado.
* Conectese a la instancia através de su terminal utilizando el par de claves descargado: ssh -i nombreclaves.pem ec2-user@IPpublica
* Instale los requerimientos del archivo "requirements.txt"
* Instale pip.
* Instale git hub.
* Clone el repositorio.
* Realice los pasos de ejecución mencionados en el numeral 3.

## Una mini guia de como un usuario utilizaría el software o la aplicación
* Utilizando los pasos del numeral 3:
- Ejecute los nodos con los que desea conformar la red.
- Utilice la ruta "joinNetwork" para conectar los nodos entre sí.
- Utilice la ruta "printMyInfo" para asegurarse de que la red está conformada.
- Utilice la ruta "uploadFile" para cargar un archivo.
- Utilice la ruta "downloadFile" para descargar un archivo
- Utilice la ruta "printMyFiles" para asegurarse que la descarga se realizó correctamente.
- Utilice la ruta "leaveNetwork" para dejar la red.

# 5. otra información que considere relevante para esta actividad.

# referencias:

## DHT-CHORD Project-https://nam10.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2FMNoumanAbbasi%2FChord-DHT-for-File-Sharing&data=05%7C02%7Cmamartin11%40eafit.edu.co%7Cbf357f732d064c11127908dcb8ce0a15%7C99f7b55e9cbe467b8143919782918afb%7C0%7C0%7C638588442287330671%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C0%7C%7C%7C&sdata=l35qnM%2FUWZoz5PG7FP%2BxLkjRbBAQYx1oSSbmqJBdh%2FU%3D&reserved=0
## Distributed Systems-https://www.distributed-systems.net/index.php/books/ds4/
## DHT-CHORD paper-https://pdos.csail.mit.edu/papers/ton:chord/paper-ton.pdf
## Finger table viedo-https://www.youtube.com/watch?v=2gCvoze-tCI&t=14s
