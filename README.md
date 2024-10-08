# 10 problemas de ensamblador

## 1 Determinar si un número dado es primo.

```
/*
 * Title: Determinador de números primos en ARM Assembly
 * Filename: primo.s
 * Autor: Sanchez Salinas Eduardo Josue
 * Date: 2024-10-02
 * Description: Programa que determina si un número es primo.
 * Input: Un número entero desde la consola.
 * Output: Mensaje indicando si el número es primo o no.
 */

.section .data
prompt: .asciz "Ingrese un número: "  @ Mensaje para solicitar un número
prime_msg: .asciz "%d es un número primo.\n"  @ Mensaje si el número es primo
not_prime_msg: .asciz "%d no es un número primo.\n"  @ Mensaje si no es primo
buffer: .space 4  @ Espacio para el número ingresado

.section .text
.globl _start
_start:
    @ Solicitar al usuario que ingrese un número
    ldr r0, =prompt         @ Cargar la dirección del mensaje
    bl printf               @ Llamar a printf para mostrar el mensaje

    @ Leer el número ingresado
    ldr r0, =buffer        @ Cargar la dirección del buffer
    bl scanf               @ Llamar a scanf para leer el número
    ldr r0, [r0]           @ Cargar el número en r0

    @ Verificar si el número es primo
    bl es_primo            @ Llamar a la función es_primo
    cmp r0, #0             @ Comparar el resultado con 0
    beq no_primo           @ Si es 0, el número no es primo

    @ Si el número es primo
    ldr r0, =prime_msg     @ Cargar mensaje de primo
    bl printf               @ Llamar a printf
    b fin                  @ Saltar al final

no_primo:
    @ Si el número no es primo
    ldr r0, =not_prime_msg  @ Cargar mensaje de no primo
    bl printf               @ Llamar a printf

fin:
    @ Salir del programa
    mov r7, #1             @ Código de syscall para exit
    svc 0                   @ Llamar al kernel

@ Función para determinar si un número es primo
es_primo:
    cmp r0, #2             @ Si el número es menor que 2
    blt not_prime_return    @ No es primo

    mov r1, #2             @ Inicializar divisor
    sqrt_loop:
        mul r2, r1, r1     @ r2 = r1 * r1
        cmp r2, r0         @ Comparar r2 con el número
        bgt prime_return   @ Si r2 > número, es primo

        mov r2, r0         @ Copiar el número a r2
        bl mod             @ Calcular el residuo

        cmp r0, #0         @ Comparar el residuo con 0
        beq not_prime_return @ Si el residuo es 0, no es primo

        add r1, r1, #1     @ Incrementar divisor
        b sqrt_loop        @ Repetir el bucle

not_prime_return:
    mov r0, #0             @ No es primo
    bx lr                  @ Regresar

prime_return:
    mov r0, #1             @ Es primo
    bx lr                  @ Regresar

@ Función para calcular el residuo (mod)
mod:
    udiv r3, r0, r1        @ r3 = número / divisor
    mul r4, r3, r1         @ r4 = r3 * divisor
    sub r0, r0, r4         @ residuo = número - r4
    bx lr                  @ Regresar

   ```
##  2 Encontrar todos los números primos menores que 100.

```
/*
 * Title: Encontrar todos los números primos menores que 100 en ARM Assembly
 * Filename: primos_menores_a_100.s
 * Autor: Sanchez Salinas Eduardo Josue
 * Date: 2024-10-02
 * Description: Programa que encuentra e imprime todos los números primos menores que 100.
 * Output: Números primos menores que 100.
 */

.section .data
primo_msg: .asciz "%d es un número primo.\n"  @ Mensaje si el número es primo

.section .text
.globl _start
_start:
    mov r0, #2          @ Comenzar desde el primer número primo

loop:
    cmp r0, #100        @ Comparar r0 con 100
    bge fin             @ Si r0 >= 100, finalizar

    bl es_primo         @ Llamar a la función es_primo
    cmp r0, #0          @ Comprobar si es primo
    beq not_prime       @ Si no es primo, saltar

    ldr r0, =primo_msg  @ Cargar mensaje de primo
    bl printf           @ Llamar a printf para mostrar el mensaje

not_prime:
    add r0, r0, #1      @ Incrementar el número
    b loop              @ Volver al inicio del bucle

fin:
    mov r7, #1          @ Código de syscall para exit
    svc 0                @ Llamar al kernel

@ Función para determinar si un número es primo
es_primo:
    cmp r0, #2          @ Si el número es menor que 2
    blt not_prime_return @ No es primo

    mov r1, #2          @ Inicializar divisor
sqrt_loop:
    mul r2, r1, r1      @ r2 = r1 * r1
    cmp r2, r0          @ Comparar r2 con el número
    bgt prime_return    @ Si r2 > número, es primo

    mov r2, r0          @ Copiar el número a r2
    bl mod              @ Calcular el residuo

    cmp r0, #0          @ Comparar el residuo con 0
    beq not_prime_return @ Si el residuo es 0, no es primo

    add r1, r1, #1      @ Incrementar divisor
    b sqrt_loop         @ Repetir el bucle

not_prime_return:
    mov r0, #0          @ No es primo
    bx lr               @ Regresar

prime_return:
    mov r0, #1          @ Es primo
    bx lr               @ Regresar

@ Función para calcular el residuo (mod)
mod:
    udiv r3, r0, r1     @ r3 = número / divisor
    mul r4, r3, r1      @ r4 = r3 * divisor
    sub r0, r0, r4      @ residuo = número - r4
    bx lr               @ Regresar
   ```
##  3 Calcular el máximo común divisor (MCD) de dos números utilizando el algoritmo de Euclides.

```
/*
 * Title: Calcular el MCD de dos números en ARM Assembly
 * Filename: CalcularMCD.s
 * Autor: Sanchez Salinas Eduardo Josue
 * Date: 2024-10-03
 * Description: Programa que calcula el MCD de dos números utilizando el algoritmo de Euclides.
 * Input: Dos números enteros desde la consola.
 * Output: El MCD de los dos números.
 */

.section .data
prompt: .asciz "Ingrese el primer número: "  @ Mensaje para el primer número
prompt2: .asciz "Ingrese el segundo número: " @ Mensaje para el segundo número
gcd_msg: .asciz "El MCD es: %d\n"               @ Mensaje de salida

.section .text
.globl _start
_start:
    @ Leer el primer número
    ldr r0, =prompt
    bl printf
    bl scanf
    ldr r1, [r0]         @ r1 = primer número

    @ Leer el segundo número
    ldr r0, =prompt2
    bl printf
    bl scanf
    ldr r2, [r0]         @ r2 = segundo número

    bl gcd               @ Llamar a la función gcd

    ldr r0, =gcd_msg     @ Mensaje de salida
    bl printf            @ Imprimir el MCD

    mov r7, #1           @ Código de syscall para exit
    svc 0                 @ Llamar al kernel

@ Función para calcular el MCD usando el algoritmo de Euclides
gcd:
    cmp r2, #0           @ Comparar r2 con 0
    beq gcd_return       @ Si es 0, regresar

    mov r3, r1           @ Copiar r1 a r3
    mov r1, r2           @ r1 = r2
    mov r2, r3           @ r2 = r3 % r2
    b gcd                @ Repetir el cálculo

gcd_return:
    bx lr                @ Regresar


   ```
##  4 Calcular el mínimo común múltiplo (MCM) de dos números.

```
/*
 * Title: Calcular el MCM de dos números en ARM Assembly
 * Filename: CalcularMCM.s
 * Autor: Sanchez Salinas Eduardo Josue
 * Date: 2024-10-03
 * Description: Programa que calcula el MCM de dos números.
 * Input: Dos números enteros desde la consola.
 * Output: El MCM de los dos números.
 */

.section .data
prompt: .asciz "Ingrese el primer número: "  @ Mensaje para el primer número
prompt2: .asciz "Ingrese el segundo número: " @ Mensaje para el segundo número
lcm_msg: .asciz "El MCM es: %d\n"               @ Mensaje de salida

.section .text
.globl _start
_start:
    @ Leer el primer número
    ldr r0, =prompt
    bl printf
    bl scanf
    ldr r1, [r0]         @ r1 = primer número

    @ Leer el segundo número
    ldr r0, =prompt2
    bl printf
    bl scanf
    ldr r2, [r0]         @ r2 = segundo número

    bl gcd               @ Llamar a la función gcd
    mul r3, r1, r2       @ r3 = r1 * r2
    udiv r3, r3, r0      @ r3 = (r1 * r2) / gcd

    ldr r0, =lcm_msg     @ Mensaje de salida
    bl printf            @ Imprimir el MCM

    mov r7, #1           @ Código de syscall para exit
    svc 0                 @ Llamar al kernel

@ Función para calcular el MCD usando el algoritmo de Euclides
gcd:
    cmp r2, #0           @ Comparar r2 con 0
    beq gcd_return       @ Si es 0, regresar

    mov r3, r1           @ Copiar r1 a r3
    mov r1, r2           @ r1 = r2
    mov r2, r3           @ r2 = r3 % r2
    b gcd                @ Repetir el cálculo

gcd_return:
    bx lr                @ Regresar

   ```
##  5 Escribir un programa que genere los primeros 100 números primos.
  
```
/*
 * Title: Generar los primeros 100 números primos en ARM Assembly
 * Filename: Primos_100.s
 * Autor: Sanchez Salinas Eduardo Josue
 * Date: 2024-10-04
 * Description: Programa que genera e imprime los primeros 100 números primos.
 * Output: Los primeros 100 números primos.
 */

.section .data
primo_msg: .asciz "%d es un número primo.\n"  @ Mensaje si el número es primo

.section .text
.globl _start
_start:
    mov r0, #2          @ Comenzar desde el primer número primo
    mov r4, #0          @ Contador de números primos

loop:
    cmp r4, #100        @ Comparar el contador con 100
    beq fin             @ Si hemos encontrado 100 primos, finalizar

    bl es_primo         @ Llamar a la función es_primo
    cmp r0, #0          @ Comprobar si es primo
    beq not_prime       @ Si no es primo, saltar

    ldr r0, =primo_msg  @ Cargar mensaje de primo
    bl printf           @ Llamar a printf para mostrar el mensaje
    add r4, r4, #1      @ Incrementar contador de primos

not_prime:
    add r0, r0, #1      @ Incrementar el número
    b loop              @ Volver al inicio del bucle

fin:
    mov r7, #1          @ Código de syscall para exit
    svc 0                @ Llamar al kernel

@ Función para determinar si un número es primo
es_primo:
    cmp r0, #2          @ Si el número es menor que 2
    blt not_prime_return @ No es primo

    mov r1, #2          @ Inicializar divisor
sqrt_loop:
    mul r2, r1, r1      @ r2 = r1 * r1
    cmp r2, r0          @ Comparar r2 con el número
    bgt prime_return    @ Si r2 > número, es primo

    mov r2, r0          @ Copiar el número a r2
    bl mod              @ Calcular el residuo

    cmp r0, #0          @ Comparar el residuo con 0
    beq not_prime_return @ Si el residuo es 0, no es primo

    add r1, r1, #1      @ Incrementar divisor
    b sqrt_loop         @ Repetir el bucle

not_prime_return:
    mov r0, #0          @ No es primo
    bx lr               @ Regresar

prime_return:
    mov r0, #1          @ Es primo
    bx lr               @ Regresar

@ Función para calcular el residuo (mod)
mod:
    udiv r3, r0, r1     @ r3 = número / divisor
    mul r4, r3, r1      @ r4 = r3 * divisor
    sub r0, r0, r4      @ residuo = número - r4
    bx lr               @ Regresar

   ```
##  6 Encontrar los factores primos de un número dado.
   
```
/*
 * Title: Encontrar los factores primos de un número en ARM Assembly
 * Filename: Factoresprimos.s
 * Autor: Sanchez Salinas Eduardo Josue
 * Date: 2024-10-04
 * Description: Programa que encuentra e imprime los factores primos de un número.
 * Input: Un número entero desde la consola.
 * Output: Los factores primos del número.
 */

.section .data
prompt: .asciz "Ingrese un número: "  @ Mensaje para solicitar un número
factor_msg: .asciz "%d es un factor primo.\n"  @ Mensaje si es factor primo

.section .text
.globl _start
_start:
    @ Leer el número
    ldr r0, =prompt
    bl printf
    bl scanf
    ldr r1, [r0]         @ r1 = número ingresado

    mov r2, #2           @ Inicializar divisor

factors_loop:
    cmp r2, r1           @ Comparar divisor con el número
    bgt fin              @ Si divisor > número, finalizar

    bl mod               @ Llamar a la función mod
    cmp r0, #0           @ Verificar si hay residuo 0
    beq is_factor        @ Si residuo es 0, es un factor

    add r2, r2, #1       @ Incrementar divisor
    b factors_loop       @ Volver al inicio del bucle

is_factor:
    ldr r0, =factor_msg  @ Cargar mensaje de factor
    bl printf            @ Imprimir el factor

    @ Dividir el número por el factor
    mov r3, r1           @ Copiar número a r3
    bl div               @ Dividir el número

    b factors_loop       @ Volver al inicio del bucle

fin:
    mov r7, #1           @ Código de syscall para exit
    svc 0                 @ Llamar al kernel

@ Función para calcular el residuo (mod)
mod:
    udiv r3, r0, r1      @ r3 = número / divisor
    mul r4, r3, r1       @ r4 = r3 * divisor
    sub r0, r0, r4       @ residuo = número - r4
    bx lr                @ Regresar

@ Función para dividir
div:
    udiv r1, r3, r2      @ r1 = número / factor
    bx lr                @ Regresar

   ```
##  7 Calcular la suma de los divisores propios de un número dado.
   
```
/*
 * Title: Calcular la suma de los divisores propios de un número en ARM Assembly
 * Filename: sumadivisores.s
 * Autor: Sanchez Salinas Eduardo Josue
 * Date: 2024-10-05
 * Description: Programa que calcula la suma de los divisores propios de un número.
 * Input: Un número entero desde la consola.
 * Output: La suma de los divisores propios.
 */

.section .data
prompt: .asciz "Ingrese un número: "  @ Mensaje para solicitar un número
sum_msg: .asciz "La suma de los divisores propios es: %d\n"  @ Mensaje de salida

.section .text
.globl _start
_start:
    @ Leer el número
    ldr r0, =prompt
    bl printf
    bl scanf
    ldr r1, [r0]         @ r1 = número ingresado

    mov r2, #1           @ Inicializar divisor
    mov r3, #0           @ Inicializar suma

sum_loop:
    cmp r2, r1           @

   ```
##  8 Determinar si un número dado es un número perfecto (la suma de sus divisores propios es igual al número).
   
```
/*
 * Title: Determinar si un número es perfecto en ARM Assembly
 * Filename: n¿NumeroPerfecto.s
 * Autor: Sanchez Salinas Eduardo Josue
 * Date: 2024-10-05
 * Description: Programa que determina si un número es un número perfecto.
 * Input: Un número entero desde la consola.
 * Output: Mensaje indicando si el número es perfecto o no.
 */

.section .data
prompt: .asciz "Ingrese un número: "  @ Mensaje para solicitar un número
perfect_msg: .asciz "%d es un número perfecto.\n"  @ Mensaje si el número es perfecto
not_perfect_msg: .asciz "%d no es un número perfecto.\n"  @ Mensaje si no es perfecto

.section .text
.globl _start
_start:
    @ Leer el número
    ldr r0, =prompt
    bl printf
    bl scanf
    ldr r1, [r0]         @ r1 = número ingresado

    mov r2, #1           @ Inicializar divisor
    mov r3, #0           @ Inicializar suma

sum_loop:
    cmp r2, r1           @ Comparar divisor con el número
    bge check_perfect     @ Si divisor >= número, comprobar si es perfecto

    bl mod               @ Llamar a la función mod
    cmp r0, #0           @ Verificar si hay residuo 0
    beq add_divisor      @ Si residuo es 0, es un divisor propio

    add r2, r2, #1       @ Incrementar divisor
    b sum_loop           @ Volver al inicio del bucle

add_divisor:
    add r3, r3, r2       @ Sumar el divisor a la suma
    add r2, r2, #1       @ Incrementar divisor
    b sum_loop           @ Volver al inicio del bucle

check_perfect:
    cmp r3, r1           @ Comparar suma de divisores con el número
    beq perfect           @ Si son iguales, es perfecto

not_perfect:
    ldr r0, =not_perfect_msg @ Cargar mensaje de no perfecto
    b print_result        @ Ir a imprimir resultado

perfect:
    ldr r0, =perfect_msg @ Cargar mensaje de perfecto

print_result:
    bl printf            @ Imprimir el resultado
    mov r7, #1           @ Código de syscall para exit
    svc 0                @ Llamar al kernel

@ Función para calcular el residuo (mod)
mod:
    udiv r3, r0, r1      @ r3 = número / divisor
    mul r4, r3, r1       @ r4 = r3 * divisor
    sub r0, r0, r4       @ residuo = número - r4
    bx lr                @ Regresar

   ```
##  9 Encontrar todos los números perfectos menores que 1000.
   
```
/*
 * Title: Encontrar números perfectos menores de 1000 en ARM Assembly
 * Filename: Perfecto1000.s
 * Autor: Sanchez Salinas Eduardo Josue
 * Date: 2024-10-06
 * Description: Programa que encuentra e imprime números perfectos menores de 1000.
 * Output: Números perfectos menores de 1000.
 */

.section .data
perfect_msg: .asciz "%d es un número perfecto.\n"  @ Mensaje si es perfecto

.section .text
.globl _start
_start:
    mov r1, #1           @ Inicializar el número

outer_loop:
    cmp r1, #1000        @ Comparar r1 con 1000
    bge fin              @ Si es >= 1000, finalizar

    mov r2, #1           @ Inicializar divisor
    mov r3, #0           @ Inicializar suma de divisores

inner_loop:
    cmp r2, r1           @ Comparar divisor con el número
    bge check_perfect     @ Si divisor >= número, comprobar

    bl mod               @ Llamar a la función mod
    cmp r0, #0           @ Verificar si es divisor
    beq add_divisor      @ Si es divisor, agregar

    add r2, r2, #1       @ Incrementar divisor
    b inner_loop         @ Volver al inicio del bucle

add_divisor:
    add r3, r3, r2       @ Sumar el divisor
    add r2, r2, #1       @ Incrementar divisor
    b inner_loop         @ Volver al inicio del bucle

check_perfect:
    cmp r3, r1           @ Comparar suma de divisores con el número
    beq print_perfect    @ Si son iguales, imprimir

    b next               @ Ir al siguiente número

print_perfect:
    ldr r0, =perfect_msg @ Cargar mensaje de perfecto
    bl printf            @ Imprimir el número perfecto

next:
    add r1, r1, #1       @ Incrementar número
    b outer_loop         @ Volver al inicio del bucle

fin:
    mov r7, #1           @ Código de syscall para exit
    svc 0                @ Llamar al kernel

@ Función para calcular el residuo (mod)
mod:
    udiv r3, r0, r1      @ r3 = número / divisor
    mul r4, r3, r1       @ r4 = r3 * divisor
    sub r0, r0, r4       @ residuo = número - r4
    bx lr                @ Regresar

   ```
##  10 Escribir un programa que determine si un número es un número de Carmichael.
   
```
/*
 * Title: Determinar si un número es de Carmichael en ARM Assembly
 * Filename:carmichael.s
 * Autor: Sanchez Salinas Eduardo Josue
 * Date: 2024-10-07
 * Description: Programa que determina si un número es un número de Carmichael.
 * Input: Un número entero desde la consola.
 * Output: Mensaje indicando si el número es de Carmichael o no.
 */

.section .data
prompt: .asciz "Ingrese un número: "  @ Mensaje para solicitar un número
carmichael_msg: .asciz "%d es un número de Carmichael.\n"  @ Mensaje si es Carmichael
not_carmichael_msg: .asciz "%d no es un número de Carmichael.\n"  @ Mensaje si no es Carmichael

.section .text
.globl _start
_start:
    @ Leer el número
    ldr r0, =prompt
    bl printf
    bl scanf
    ldr r1, [r0]         @ r1 = número ingresado

    @ Verificar si es Carmichael
    bl es_carmichael      @ Llamar a la función es_carmichael
    cmp r0, #1           @ Comprobar resultado
    beq not_carmichael    @ Si no es Carmichael, ir a not_carmichael

carmichael:
    ldr r0, =carmichael_msg @ Cargar mensaje de Carmichael
    b print_result        @ Ir a imprimir resultado

not_carmichael:
    ldr r0, =not_carmichael_msg @ Cargar mensaje de no Carmichael

print_result:
    bl printf            @ Imprimir el resultado
    mov r7, #1           @ Código de syscall para exit
    svc 0                @ Llamar al kernel

@ Función para determinar si un número es de Carmichael
es_carmichael:
    @ Implementar la lógica para determinar si r1 es un número de Carmichael
    bx lr                @ Regresar (debe incluir lógica aquí)

   ```
