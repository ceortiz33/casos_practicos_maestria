# Modulo 5

## Enunciado

Se ha obtenido un fichero binario ejecutable que, tras su ejecución, muestra un texto con un código numérico. Este código se genera a partir de una cadena de texto guardada en el propio binario. Tras listar el código ASM, el fichero binario se eliminó y no es posible acceder a él, solo al código ASM copiado al final del ejercicio.

Se necesita poder reutilizar dicho algoritmo de generación de códigos, por lo que se requieren labores de ingeniería inversa para analizar el binario y reconstruir el  código fuente de tal forma que se pueda modificar y volver a compilar correctamente.

## Instrucciones

1. Divide el códigoen basic blocks.
2. Realiza el diagrama de flujo con los basic blocks.
3. ¿Existe alguna estructura de control? Indica que basic block intervienen en ella
4. Convierte el código completo de la función en código c
    - Con lo aprendido sobrereconstrucciónde código,convierte esta funciónmain() en código C
    - En <+36> se carga en eax la direcciónde la cadena indicada en negrita. **“3jd9cjfk98hnd”**
    - En <+110> se carga en eax la dirección de la cadena indicada `“[+] Codigo generado: %i\n”` 
5. Compila el código generado e indica el código resultante tras su ejecución.Compila en 32 bits agregando la opción--32 tal y como se indica en el siguiente comando:$gcc source.c -o source -m32
6. Modifica el código Fuente en C, para que genere un nuevo códigoa partir de otra cadena.
    - Modifica la cadena <+36> en el códigoC, por la siguiente cadena:“Congratulations!”
    - Compila el código C, ejecuta e indica eltexto completo obtenido

### Dump of assembler code for function main:

``` 
0x0000054d <+0>: lea ecx,[esp+0x4]
0x00000551 <+4>: and esp,0xfffffff0
0x00000554 <+7>: push DWORD PTR [ecx-0x4]
0x00000557 <+10>: push ebp
0x00000558 <+11>: mov ebp,esp
0x0000055a <+13>: push ebx
0x0000055b <+14>: push ecx
0x0000055c <+15>: sub esp,0x10
0x0000055f <+18>: call 0x450 <__x86.get_pc_thunk.bx>
0x00000564 <+23>: add ebx,0x1a9c
0x0000056a <+29>: mov DWORD PTR [ebp-0x10],0x0
0x00000571 <+36>: lea eax,[ebx-0x19a0] ; “3jd9cjfk98hnd”
0x00000577 <+42>: mov DWORD PTR [ebp-0x14],eax
0x0000057a <+45>: sub esp,0xc
0x0000057d <+48>: push DWORD PTR [ebp-0x14]
0x00000580 <+51>: call 0x3e0 <strlen@plt>
0x00000585 <+56>: add esp,0x10
0x00000588 <+59>: mov DWORD PTR [ebp-0x18],eax
0x0000058b <+62>: mov DWORD PTR [ebp-0xc],0x0
0x00000592 <+69>: jmp 0x5ad <main+96>
0x00000594 <+71>: mov edx,DWORD PTR [ebp-0xc]
0x00000597 <+74>: mov eax,DWORD PTR [ebp-0x14]
0x0000059a <+77>: add eax,edx
0x0000059c <+79>: movzx eax,BYTE PTR [eax]
0x0000059f <+82>: movsx eax,al
0x000005a2 <+85>: imul eax,DWORD PTR [ebp-0x18]
0x000005a6 <+89>: add DWORD PTR [ebp-0x10],eax
0x000005a9 <+92>: add DWORD PTR [ebp-0xc],0x1
0x000005ad <+96>: mov eax,DWORD PTR [ebp-0xc]
0x000005b0 <+99>: cmp eax,DWORD PTR [ebp-0x18]
0x000005b3 <+102>: jl 0x594 <main+71>
0x000005b5 <+104>: sub esp,0x8
0x000005b8 <+107>: push DWORD PTR [ebp-0x10]
0x000005bb <+110>: lea eax,[ebx-0x1992] ; “[+] Codigo generado: %i\n”
0x000005c1 <+116>: push eax
0x000005c2 <+117>: call 0x3d0 <printf@plt>
0x000005c7 <+122>: add esp,0x10
0x000005ca <+125>: mov eax,0x0
0x000005cf <+130>: lea esp,[ebp-0x8]
0x000005d2 <+133>: pop ecx
0x000005d3 <+134>: pop ebx
0x000005d4 <+135>: pop ebp
0x000005d5 <+136>: lea esp,[ecx-0x4]
0x000005d8 <+139>: ret
```

1. De  acuerdo  al código anterior se muestran los siguientes bloques básicos. El primer bloque corresponde hasta **<+69>** donde se produce un salto a otra dirección de memoria en **<+96>**

**Bloque 1**

![](/images/modulo5/bloque1.PNG)

  Al realizar dicho salto se produce una sentencia de comparación entre dos variables y en caso de que ocurriese que la variable 1 es menor a la variable 2 se produce un salto a **<+71>**
  
 **Bloque 2**
 
 ![](/images/modulo5/bloque2.PNG)
 
El siguiente bloque corresponde a lo que pasaría si el resultado de la comparación anterior fuera true, es decir se ejecuta todo este bloque desde **<+71>** hasta  **<+92>** siendo la siguiente instrucción **<+96>** dando como resultado un bucle del cual no se saldrá hasta que el resultado de la comparación sea false. 

**Bloque 3**

![](/images/modulo5/bloque3.PNG)

Finalmente se logra salir del bucle y las siguientes instrucciones en ser ejecutadas son las siguientes.

**Bloque 4**

![](/images/modulo5/bloque4.PNG)

2. Para crear una representaciónde un diagrama de flujo con los bloques básicos se crean relaciones de la siguiente manera

**Diagrama de flujo**

![](/images/modulo5/diagramadeflujo.PNG)

**Estructura de bloques en IDA**

![](/images/modulo5/bloquesida.PNG)

**Primer Bloque IDA**

![](/images/modulo5/primerbloqueida.PNG)

**Segundo, Tercer y Cuarto bloque en IDA**

![](/images/modulo5/esquema.PNG)

3. Si, en efecto existe una estructura de control que corresponde a un lazo for, los bloques que intervienen son el bloque comparador que salta a **<+71>** cuando la condición del bucle es **true** y el bloque de instrucciones que corresponde a las instrucciones siguientes a **<+71>** que repiten su operación hasta que la variable1 sea mayor a la variable2.

4. Al convertir el código mostrado a código en lenguaje C se obtuvo lo siguiente

```c
#include <stdio.h>
#include <string.h>

int main(){
   int a=0;
   char *cadena="3jd9cjfk98hnd";
   int b=strlen(cadena);
   
for(int d=0;d<b;d++){
   a = a + (cadena[d] * b);
  }
}
```

Para llegar a esta solución que utilizaron las librerías **<stdio.h>** para los resultados de entrada y salida y **<string.h>** para mostrar las cadenas. El propio ensamblador le ha asignado las siguientes direcciones de memoria a las variables

+ **a**  -> DWORD PTR[ebp-0xc]
+ **b**  -> DWORD PTR[ebp-0x18]
+ **d**  -> DWORD PTR[ebp-0x14]
+ **cadena** -> DWORD PTR[ebp-0x10]

A continuación,una breve descripción de cómo se llegó a la solución. Se declara la variable a inicializada en 0.

`mov DWORD PTR[ebp-0xc], 0x0`

Se declara el puntero char con la cadena “3jd9cjfk98hnd”.

```
lea eax, [ebx-0x1ff8]
mov DWORD PTR[ebp-0x10],eax
```

Se crea una variable de tipo int b para almacenar el resultado de la función strlen que devuelve el resultado del número de caracteres de una cadena.

```
sub exp,0xc
push DWORD PTR[ebp-0x10]
call 0x56556040 <strlen@plt>
add esp,0x10
mov DWORD PTR[ebp-0x18], eax
```

Las siguientes instrucciones corresponden ala estructura del lazo `for(int d =0; d<b;d++)`.

```
<+62> mov DWORD PTR[ebp-0x14],0x0
<+69> jmp  0x5655620a <main +97>
<+93> add DWORD PTR[ebp-0x14],0x1
<+97> mov eax, DWORD PTR[ebp-0x14]
<+100> cmp eax,DWORD PTR[ebp-0x18]
<+103> jl  0x565561f0 <main+71>
```

Se realiza un desplazamiento en los indices **d** del lazo for para cada elemento de la cadena `cadena[d]`

```
<+71> mov eax, DWORD PTR[ebp-0x14]
<+74> add DWORD PTR[ebp-0x10],eax
<+77> mov eax,DWORD PTR[ebp-0x10]
<+80> movzx eax,BYTE PTR[eax]
<+83> movsx eax,al
```

El resultado de eax seria `cadena[d]`, en estas instrucciones se realiza la multiplicacion de la variable b con eax, para luego sumar ese resultado a la variable **a** y dando como resultado la instruccion `a = a + (cadena[d] * b);`

```
<+86> imul eax,DWORD PTR[ebp-0x18]  
<+90> add DWORD PTR[ebp-0xc],eax
```

Se carga el resultado de la variable a haciala cadena “[+] Código generado: %i\n”.

```
<+105> sub esp,0x8
<+108> push DWORD PTR[ebp-0xc]
<+111> lea eax,[ebx-0x1fea]
<+117> push eax
<+118> call 0x56556030 <printf@plt>
<+123> add esp,0x10
<+126> mov eax,0x0
```

5. El resultado de compilar el código es el siguiente:

**Codigo Compilado Resultante**

![](/images/modulo5/codigocompilado.PNG)

6. Al modificar la cadena en <+36> del código C anterior se produce lo siguiente.

```c
#include <stdio.h>
#include <string.h>

int main(){
   int a=0;
   char *cadena="Congratulations!";
   int b=strlen(cadena);
   
for(int d=0;d<b;d++){
   a = a + (cadena[d] * b);
  }
  
printf("[+] Codigo generado: %i\n",a);  
}
```

El código en ensamblador es idéntico al anterior, lo que varía es la respuesta del código generado como se muestra a continuación.

**Nuevo codigo compilado**

![](/images/modulo5/nuevocompilado.PNG)

El código generado por la cadena “Congratulations!” es **12576**

**Resultados cadena "Congratulations!"**

![](/images/modulo5/nuevoresultado.PNG)

Código generado porla cadena “3jd9cjfk98hnd”.

**Resultado cadena "3jd9cjfk98hnd"**

![](/images/modulo5/resultado.PNG)

## Conclusion

Con base a los resultados anteriores se puede mencionar lo siguiente. El código generado depende de la longitud de la cadena, en el caso de “Congratulations!” esta tiene una longitud de 14 caracteres, dadas las condiciones del lazo for, esta se ejecutaría 13 veces antes de salir del bucle, en el caso de la cadena “3jd9cjfk98hnd” el lazo for se ejecuta 12 veces. Otra observación a tener en cuenta es que cuando se utilizan únicamente letras y símbolos este valor se incrementa en mayor proporción que si se utilizara una combinación de letras minúsculas y números.





