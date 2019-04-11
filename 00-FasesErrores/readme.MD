
# TP #1 "Fases de la Traducción y Errores"

Enunciado:
> Identificar las distintas fases de traducción y errores que pueden aparecer
___
Para el archivo `hello2.c`
```c
  #include <stdio.h>
  int/*DENUVO REMUVO*/main(void){
    int i=42;
    prontf("La respuesta es %d\n");
```
se ejecuta el comando `gcc hello2.c -E -o hello2.i` y el archivo resultante `hello2.i` tiene simplemente
copiado el contenido de *stdio* donde estaba la linea `include`, hay aproximadamente 500 lineas nuevas.

El comentario `/*DENUVO REMUVO*/` fué reemplazado por un espacio, evidenciado por la separación resultante entre `int` y `main(void){`.
___
Para el archivo `hello3.c`
```c
int printf(const char *s, ...);
int main(void){
    int i=42;
    prontf("La respuesta es %d\n");
```
Investigar la semántica de la linea `int printf(const char *s, ...);`
* Es la declaración de una función
* La función devuelve un número y tiene un número variable de parámetros, que son punteros a un carácter.

Luego con el comando `gcc hello3.c -E -o hello3.i` resulta un archivo `hello3.i`.

La única diferencia son 4 lineas nuevas al comienzo del archivo
```c
# 1 "hello3.c"
# 1 "<built-in>"
# 1 "<command-line>"
# 1 "hello3.c"
```
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
Se corrige `prontf` por `printf` y se cierra el `{`.

El archivo `hello4.c` queda
```c
int  printf(const  char  *s, ...);
int  main(void){
	int i=42;
	printf("La respuesta es %d\n");
}
```
Se utiliza el comando `gcc hello4.c -S` y se obtiene el archivo `hello4.s`
```assembly
	.file	"hello4.c"
	.text
	.def	___main;	.scl	2;	.type	32;	.endef
	.section .rdata,"dr"
LC0:
	.ascii "La respuesta es %d\12\0"
	.text
	.globl	_main
	.def	_main;	.scl	2;	.type	32;	.endef
_main:
LFB0:
	.cfi_startproc
	pushl	%ebp
	.cfi_def_cfa_offset 8
	.cfi_offset 5, -8
	movl	%esp, %ebp
	.cfi_def_cfa_register 5
	andl	$-16, %esp
	subl	$32, %esp
	call	___main
	movl	$42, 28(%esp)
	movl	$LC0, (%esp)
	call	_printf
	movl	$0, %eax
	leave
	.cfi_restore 5
	.cfi_def_cfa 4, 4
	ret
	.cfi_endproc
LFE0:
	.ident	"GCC: (MinGW.org GCC-8.2.0-3) 8.2.0"
	.def	_printf;	.scl	2;	.type	32;	.endef
```
Investigar el archivo `hello4.s`
___
