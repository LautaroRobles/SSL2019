

# TP #1 "Fases de la Traducción y Errores"

Enunciado:
> Identificar las distintas fases de traducción y errores que pueden aparecer
___
> (1) Escribir `hello2.c`, que es una variante de `hello.c`
```c
  #include <stdio.h>
  int/*DENUVO REMUVO*/main(void){
    int i=42;
    prontf("La respuesta es %d\n");
```
> (2) Pre procesar `hello2.c`, no compilar, y generar `hello2.i`. Analizar su contenido. 

Se ejecuta el comando `gcc hello2.c -E -o hello2.i` y el archivo resultante `hello2.i` tiene simplemente
copiado el contenido de *stdio* donde estaba la linea `include`, hay aproximadamente 500 lineas nuevas.

El comentario `/*DENUVO REMUVO*/` fué reemplazado por un espacio, evidenciado por la separación resultante entre `int` y `main(void){`.
___
> (3) Escribir `hello3.c`, una nueva variante:
```c
int printf(const char *s, ...);
int main(void){
    int i=42;
    prontf("La respuesta es %d\n");
```
> (4) Investigar la semántica de la linea `int printf(const char *s, ...);`
* Es la declaración de una función
* La función devuelve un número y tiene un número variable de parámetros, que son punteros a un carácter.
> (5) Pre procesar `hello3.c`, no compilar, y generar `hello3.i`. 

Con el comando `gcc hello3.c -E -o hello3.i` resulta un archivo `hello3.i`.
> Buscar diferencias entre `hello3.c` y `hello3.i`.

La única diferencia son 4 lineas nuevas al comienzo del archivo
```c
# 1 "hello3.c"
# 1 "<built-in>"
# 1 "<command-line>"
# 1 "hello3.c"
```
> (6) Compilar el resultado y generar `hello3.s`, no ensamblar

Se usa el siguiente comando `gcc hello3.i -S`.

La consola devuelve el siguiente mensaje de aviso
```
hello3.c: In function 'main':
hello3.c:4:5: warning: implicit declaration of function 'prontf'; did you mean 'printf'? [-Wimplicit-function-declaration]
     prontf("La respuesta es %d\n");
     ^~~~~~
     printf
hello3.c:4:5: error: expected declaration or statement at end of input
```
___
> (7) Corregir en el nuevo archivo `hello4.c` y empezar de nuevo, generar `hello4.s`, no ensamblar.

Se corrige el error y se cierra la llave al final del código `{`.

El archivo `hello4.c` queda
```c
int printf(const char *s, ...);
int main(void){
    int i=42;
    prontf("La respuesta es %d\n");
}
```
Se utiliza el comando `gcc hello4.c -S` y se obtiene el archivo `hello4.s`
> (8) Investigar el archivo `hello4.s`
```assembly
	.file "hello4.c"
	.text
	.def ___main; .scl 2; .type 32; .endef
	.section .rdata,"dr"
LC0:
	.ascii "La respuesta es %d\12\0"
	.text
	.globl _main
	.def _main; .scl 2; .type 32; .endef
_main:
LFB0:
	.cfi_startproc
	pushl %ebp
	.cfi_def_cfa_offset 8
	.cfi_offset 5, -8
	movl %esp, %ebp
	.cfi_def_cfa_register 5
	andl $-16, %esp
	subl $32, %esp
	call ___main
	movl $42, 28(%esp)
	movl $LC0, (%esp)
	call _prontf
	movl $0, %eax
	leave
	.cfi_restore 5
	.cfi_def_cfa 4, 4
	ret
	.cfi_endproc
LFE0:
	.ident "GCC: (MinGW.org GCC-8.2.0-3) 8.2.0"
	.def _prontf; .scl 2; .type 32; .endef
```
Se puede ver que en el código Assembly hace una llamada a `_prontf` pero no está escrito que hace. Por lo tanto lo va a buscar en la biblioteca estándar y no lo va a encontrar.
> (9) Ensamblar `hello4.s` en `hello4.o` sin vincular.

Se usa el comando `gcc hello4.s -c` y resulta en un archivo `hello4.o`
> (10) Vincular `hello4.o` con la biblioteca estándar y generar el ejecutable.

Se usa el comando `gcc hello4.o` y la consola devuelve el error
```
c:/mingw/bin/../lib/gcc/mingw32/8.2.0/../../../../mingw32/bin/ld.exe:
hello4.o:
hello4.c:(.text+0x1e): undefined reference to `prontf'
collect2.exe: error: ld returned 1 exit status
```
___
> (11) Corregir en `hello5.c` y generar el ejecutable.

Se corrige cambiando la línea `prontf("La respuesta es %d\n")` por `printf("La respuesta es %d\n")`, de esta manera se mostrará la respuesta correcta. Quedando:
```c
int  printf(const  char  *s, ...);
int  main(void){
	int i=42;
	printf("La respuesta es %d\n");
}
```
> (12) Ejecutar y analizar el resultado.

Se compila con el comando `gcc hello5.c` y el ejecutable devuelve `La respuesta es 4200640`.
___
> (13) Corregir en `hello6.c` y empezar de nuevo

Se corrige cambiando agregando la variable `i` como parámetro de `printf` quedando `printf("La respuesta es %d\n",i)`, de esta manera se mostrará la respuesta correcta. Quedando:
```c
int  printf(const  char  *s, ...);
int  main(void){
	int i=42;
	printf("La respuesta es %d\n",i);
}
```
Al compilar con el comando `gcc hello6.c` y al ejecutar devuelve `La respuesta es 42`.
___
> (14) Escribir `hello7.c`, una nueva variante:
```c
int main(void){
	int i=42;
	printf("La respuesta es %d\n", i);
}
```
> (15) Explicar porqué funciona

Funciona porque en `C` no es necesario declarar la función antes de llamarla.
Pero el compilador no tiene idea de qué devuelve la función así que asume que es un `int`. El compilador tampoco sabe qué parámetros recibe la función, entonces no se puede preparar sabiendo que debe ir un puntero a un `char`.
