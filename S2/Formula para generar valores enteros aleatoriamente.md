### Generación de valores enteros aleatorios distribuidos uniformemente
Prompt: "Genera para Java una formula que contenga Math.random y Math.round que
permita crear valores enteros entre dos limites A y Bdados, de forma que las
probabilidades de obtención de cada valor seanequivalentes, es decir, tener muy
en cuenta como se realizan los redondeos para ello"

Para generar números enteros entre dos límites **A** y **B** (ambos inclusive)
con la misma probabilidad usando `Math.round()`, la fórmula exacta en Java es:

java

    
    
    int numeroAleatorio = (int) Math.round(Math.random() * (B - A + 1) + A - 0.5);
    

Usa el código con precaución.

⚠️ El peligro del redondeo tradicional

Si utilizaras la fórmula intuitiva `Math.round(Math.random() * (B - A) + A)`,
los valores extremos **A** y **B** tendrían **la mitad de probabilidad** de
salir en comparación con los números intermedios.

Esto ocurre porque `Math.round()` corta los rangos a la mitad:

  * El extremo **A** solo se obtiene con números entre `A` y `A + 0.49`. (Rango de 0.5)

  * Un número intermedio se obtiene desde `X - 0.5` hasta `X + 0.49`. (Rango completo de 1.0)

  * El extremo **B** solo se obtiene entre `B - 0.5` y `B`. (Rango de 0.5)

💡 ¿Por qué funciona esta solución?

Al multiplicar por `(B - A + 1)` y restar `0.5`, desplazamos uniformemente
todo el espectro de valores generados por `Math.random()`.

Si tomamos como ejemplo un rango de **A = 1** a **B = 3** :

  * La fórmula se convierte en: `Math.random() * 3 + 0.5`

  * Generará un número decimal continuo en el rango de **[0.5, 3.5)**.

Al aplicar `Math.round()`, los rangos de probabilidad se distribuyen
equitativamente con un ancho exacto de 1.0 para cada número:

  * Valores de **0.5 a 1.499...** se redondean a **1** (Probabilidad: 1/3)

  * Valores de **1.5 a 2.499...** se redondean a **2** (Probabilidad: 1/3)

  * Valores de **2.5 a 3.499...** se redondean a **3** (Probabilidad: 1/3)

¿Te gustaría ver cómo implementar esto dentro de una **función reutilizable**
o prefieres analizar cómo se haría utilizando la clase **`java.util.Random`**
, que suele ser más eficiente para enteros?
