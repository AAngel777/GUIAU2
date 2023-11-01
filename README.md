# GUIAU2
Guia de examen
3.1.1. La pila y las instrucciones ldm y stm
Se denomina pila de programa a aquella zona de memoria, organizada de forma
LIFO (Last In, First Out), que el programa emplea principalmente para el almacenamiento temporal de datos. Esta pila, definida en memoria, es fundamental para
el funcionamiento de las rutinas1, aspecto que se desarrollará en esta práctica.

El puntero de pila es r13 aunque por convención nunca se emplea esa nomeclatura, sino que lo llamamos sp (stack pointer o puntero de pila). Dicho registro
apunta siempre a la palabra de memoria que corresponde a la cima de la pila (última palabra introducida en ella).

La pila tiene asociadas dos operaciones: push (meter un elemento en la pila) y
pop (sacar un elemento de la pila). En la operación push primero decrementamos
en 4 (una palabra son 4 bytes) el registro sp y luego escribimos dicho elemento en
la posición de memoria apuntada por sp. Decimos que la pila crece hacia abajo, ya
que cada vez que insertamos un dato el sp se decrementa en 4.


3.3.1. Mínimo de un vector
Dado un vector de enteros y su longitud, escribe una función en ensamblador
que recorra todos los elementos del vector y nos devuelva el valor mínimo. Para
comprobar su funcionamiento, haz .global la función y tras ensamblarla, enlázala
con este programa en C
# include < stdio.h >
int vect []= { 8, 10, - 3, 4, - 5, 50, 2, 3 };
void main ( void ){
printf (" %d\ n", minimo ( vect, 8 ));
}

4.1.1. Librerías y Kernel, las dos capas que queremos saltarnos
Una primera capa se encuentra en la librería runtime que acompaña al ejecutable, la cual incluye sólamente el fragmento de código de la función que necesitemos, en
este caso en printf. El resto de funciones de la librería (stdio), si no las invocamos no aparecen en el ejecutable. El enlazador se encarga de todo esto, tanto de ubicar
las funciones que llamemos desde ensamblador, como de poner la dirección numérica correcta que corresponda en la instrucción bl printf.

Este fragmento de código perteneciente a la primera capa sí que podemos depurarlo mediante gdb. Lo que hace es, a parte del formateo que realiza la propia función,
trasladar al sistema operativo una determinada cadena para que éste lo muestre por pantalla. Es una especie de traductor intermedio que nos facilita las cosas. Nosotros
desde ensamblador también podemos hacer llamadas al sistema directamente como veremos posteriormente.

4.1.2. Ejecutar código en Bare Metal
El ciclo de ensamblado y enlazado es distinto en un programa Bare Metal. Hasta ahora hemos creado ejecutables, que tienen una estructura más compleja, con cabecera y distintas secciones en formato ELF. Toda esta información le viene muy bien al sistema operativo, pero en un entorno Bare Metal no disponemos de él. Lo
que se carga en kernel.img es un binario sencillo, sin cabecera, que contiene directamente el código máquina de nuestro programa y que se cargará en la dirección de
RAM 0x8000.

Lo que para un ejecutable hacíamos con esta secuencia.
as -o ejemplo.o ejemplo.s
gcc -o ejemplo ejemplo.o
En caso de un programa Bare Metal tenemos que cambiarla por esta otra.
as -o ejemplo.o ejemplo.s
ld -e 0 - Ttext = 0x8000 -o ejemplo.elf ejemplo.o
objcopy ejemplo.elf -O binary kernel.img

4.2. Acceso a periféricos
Los periféricos se controlan leyendo y escribiendo datos a los registros asociados
o puertos de E/S. No confundir estos registros con los registros de la CPU. Un
puerto asociado a un periférico es un ente, normalmente del mismo tamaño que el
ancho del bus de datos, que sirve para configurar diferentes aspectos del mismo. No
se trata de RAM, por lo que no se garantiza que al leer de un puerto obtengamos
el último valor que escribimos. Es más, incluso hay puertos que sólo admiten ser
leídos y otros que sólo admiten escrituras. La funcionalidad de los puertos también
es muy variable, incluso dentro de un mismo puerto los diferentes bits del mismo
tienen distinto comportamiento.

-GPLEVn. Estos puertos devuelven el valor del pin respectivo. Si dicho pin está
en torno a 0V devolverá un cero, si está en torno a 3.3V devolverá un 1.
-GPEDSn. Sirven para detectar qué pin ha provocado una interrupción en caso
de usarlo como lectura. Al escribir en ellos también podemos notificar que ya
hemos procesado la interrupción y que por tanto estamos listos para que nos
vuelvan a interrumpir sobre los pines que indiquemos.
-GPRENn. Con estos puertos enmascaramos los pines que queremos que provoquen una interrupción en flanco de subida, esto es cuando hay una transición
de 0 a 1 en el pin de entrada.
-GPFENn. Lo mismo que el anterior pero en flanco de bajada.

