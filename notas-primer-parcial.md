---
geometry: margin=0.7in
pdf-engine: xelatex
---

# Convención de llamada linux x86_64

## Parámetros y valores de retorno 64 bits

- **Enteros y punteros**: RDI, RSI, RDX, RCX, R8, R9
- **Flotantes**: XMM0, ... , XMM7
- **Retorno**: RAX, XMM0
- **Temporales**: RAX, R10, R11, XMM8, ..., XMM15, st2, ..., st7, k0, ..., k7
- **long doubles (temporales)**: st0, st1

 **No volatiles**: RBX, RBP, R12, R13, R14, R15

Las funcioens llamadas si quieren modificar registros no volatiles tienen la obligación (por convención) de restaurarlos al terminar.

Los parametros que entran por registros se pasan de izquierda a derecha. Los que no alcanzan a entrar, se pasan por stack de derecha a izquierda (viendolo desde la declaración de la función).

Para llamadas a funciones de C, se necesita la pila alineada a 16 bytes (en 32 bits también)

## Parámetros y valores de retorno 32 bits

- Todos los parámetros se pasan por pila (de derecha a izquierda)
- **Retorno**: EAX
- **No volatiles**: EBX, EBP, ESI, EDI

## Modos de acceso a memoria
- [ inmediato ]
- [ registro ]
- [ registro + registro*escala ] siendo escala 1, 2, 4 u 8
- [ registro + inmediato ]
- [ reg + reg*escala + inm ]

![](./imagenes/registros.png)

## Alineación de structs
- Cada variable debe estar alineada a una pocisión multiplo de su tamaño.
- El tamaño de la estructura debe estar alineado al tamaño del atributo más grande
- En ambos casos se agrega padding para rellenar ( se puede sacar con **\_\_attribute\_\_((_\_packed_\_))**)

![](./imagenes/padding.png){ width=50% }

## Interacción con C
- Las funciones exportadas se deben declarar en la sección .text con ***global func***
- Las funciones de C llamadas desde ASM se deben declararen .text con ***extern func***

## Secciones del código
- **.data**: variables globales inicializadas (DB: define byte, DW: word, DD: double word, DQ: quad word)
- **.rodata**: constantes globales inicializadas (DB, DW, DD, DQ)
- **.bss**: variables globales no inicializadas (RESB, RESW, RESD, RESQ) (reserve)
- **.text**: codigo

Dentro de *.text* la etiqueta _start sería el equivalente a la función *main* <br><br>

Para ensamblar un mismo valor repetido: <br>
*"etiqueta"* **times** *"numero"* DB/BW/DD/DQ *"hexa/entero/binario/octal"*  

En general las instrucciones son registro-registro; registro-memoria; registro-inmediato; memoria-registro; memoria-inmediato


# SIMD
## MOV r-m m-r
![](./imagenes/simd-mov.png){ width=75% }

## Packed MOV r-r r-m

![](./imagenes/simd-packedmov.png){ width=75% }

![ ejemplo word to dw ](./imagenes/simd-pmov-wd.png){width=50%}
![ ejemplo dw to q ](./imagenes/simd-pmov-dq.png){width=50%}

## Packed operaciones aritmeticas r-r r-m
![](./imagenes/simd-op-aritmeticas.png){ width=60% } 


Notar que pmul tiene low y high, con low se guarda el resultado de la parte baja o alta (al multiplicar en el peor caso se necesita el doble de bits)

![](./imagenes/simd-pmul.png){ width=50% }

![](./imagenes/simd-pabs.png){ width=40% }

## Packed operaciones fp r-r r-m
![](imagenes/simd-pop-fp.png){ width=75% }

![](imagenes/simd-pop-fp2.png){ width=75% }

## Packed operaciones saturadas con enteros r-r r-m
![](imagenes/simd-pstarurado.png){ width=50% }

## Packed operaciones horizontales r-r r-m
![](imagenes/simd-p-op-horizontal.png){ width=90% }

## Packed operaciones lógicas y shifts r-r r-m
![](imagenes/simd-pop-logicas.png){ width=90% }

## Packed compare enteros y flotantes r-r r-m
![](imagenes/simd-pcmp-enteros.png){ width=90% }

![](imagenes/simd-pcmp-enteros-ejemplo.png){ width=50% }

![](imagenes/simd-pcmp-fp.png){ width=90% }

## Desenpaquetado 
Notar que hay para tomar los lows y highs

![](imagenes/simd-desempaquetado.png){ width=75% }