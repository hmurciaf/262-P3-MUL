# Listas secuenciales implementadas mediante arreglos en Java

**Programación III – Ingeniería Multimedia**  
**Tema:** Implementación de listas secuenciales mediante arreglos

---

# 1. Introducción

En programación es frecuente necesitar almacenar varios elementos de un mismo tipo de dato. La primera herramienta que ofrece Java para lograrlo son los **arreglos (arrays)**.

Sin embargo, un arreglo por sí solo **no constituye una lista**. Un arreglo únicamente reserva un espacio fijo de memoria donde pueden almacenarse elementos. Una **lista secuencial**, por el contrario, es una estructura de datos que además incorpora reglas y operaciones que permiten administrar dichos elementos.

Entre las operaciones más comunes se encuentran:

- insertar elementos;
- eliminar elementos;
- buscar elementos;
- modificar elementos;
- conocer cuántos elementos contiene la lista;
- determinar si la lista está vacía o llena.

En este documento se estudiará cómo construir una lista secuencial utilizando únicamente un arreglo simple, sin emplear clases ya implementadas por Java como **ArrayList**, ya que el objetivo es comprender el funcionamiento interno de esta estructura de datos.

---

# 2. Primer ejemplo: utilizando únicamente un arreglo

Supongamos que deseamos almacenar números enteros.

La forma más sencilla consiste en crear un arreglo y una variable que indique cuántas posiciones realmente están ocupadas.

```java
public class Main {

    public static void main(String[] args) {

        int[] lista = new int[10];
        int cantidad = 0;

        lista[cantidad++] = 15;
        lista[cantidad++] = 8;
        lista[cantidad++] = 32;
        lista[cantidad++] = 20;

        System.out.println("Contenido de la lista:");

        for (int i = 0; i < cantidad; i++) {
            System.out.println(lista[i]);
        }

    }

}
```

La salida será

```
15
8
32
20
```

Observe que el arreglo posee capacidad para diez elementos, pero únicamente cuatro contienen información válida.

Por esta razón es indispensable mantener una variable adicional (`cantidad`) que indique el número real de elementos almacenados.

---

# 3. ¿Por qué no recorrer todo el arreglo?

Si recorremos las diez posiciones, obtendremos:

```
15
8
32
20
0
0
0
0
0
0
```

Los últimos ceros **no pertenecen a la lista**.

Simplemente corresponden al valor por defecto que Java asigna a cada posición del arreglo.

Por ello, siempre debemos recorrer únicamente las posiciones comprendidas entre:

```
0
```

y

```
cantidad - 1
```

---

# 4. Insertar un elemento

Agregar un nuevo elemento consiste simplemente en escribirlo en la siguiente posición libre.

```java
lista[cantidad] = 40;
cantidad++;
```

O de manera equivalente:

```java
lista[cantidad++] = 40;
```

Después de esta operación, la lista contiene

```
15
8
32
20
40
```

---

# 5. Buscar un elemento

Una búsqueda secuencial consiste en recorrer la lista hasta encontrar el valor.

```java
int buscado = 32;
boolean encontrado = false;

for (int i = 0; i < cantidad; i++) {

    if (lista[i] == buscado) {
        encontrado = true;
        break;
    }

}

System.out.println(encontrado);
```

---

# 6. Eliminar un elemento

Supongamos que deseamos eliminar el número 8.

Inicialmente tenemos

```
15
8
32
20
40
```

El arreglo realmente contiene

```
Índice

0   1   2   3   4

15  8  32  20  40
```

Después de eliminar el 8 debemos desplazar todos los elementos hacia la izquierda.

```
15 32 20 40
```

El algoritmo sería

```java
int posicion = 1;

for (int i = posicion; i < cantidad - 1; i++) {
    lista[i] = lista[i + 1];
}

cantidad--;
```

---

# 7. Problema del enfoque anterior

Aunque el programa funciona correctamente, presenta varios inconvenientes.

Cada vez que necesitemos trabajar con una lista deberemos volver a escribir código para:

- insertar;
- eliminar;
- buscar;
- imprimir;
- verificar si está llena;
- verificar si está vacía.

Esto produce programas repetitivos.

Además, si descubrimos un error en alguno de estos algoritmos, deberemos corregirlo en todos los programas donde aparezca.

Desde el punto de vista de la Ingeniería de Software, esto constituye una mala práctica.

---

# 8. La importancia del encapsulamiento

Uno de los principios fundamentales de la Programación Orientada a Objetos consiste en **encapsular**.

Encapsular significa reunir en una misma clase:

- los datos;
- las operaciones que manipulan dichos datos.

En lugar de que el programa principal conozca cómo funciona internamente una lista, simplemente utilizará los servicios que ésta ofrece.

En otras palabras:

Antes:

```
Main
 ├── arreglo
 ├── cantidad
 ├── insertar
 ├── eliminar
 ├── buscar
 ├── imprimir
 └── ...
```

Después:

```
Main
        │
        ▼
ListaSecuencial
 ├── arreglo
 ├── cantidad
 ├── insertar()
 ├── eliminar()
 ├── buscar()
 ├── obtener()
 ├── imprimir()
 └── ...
```

El programa principal deja de preocuparse por los detalles internos.

---

# 9. Diseñando una clase ListaSecuencial

Ahora construiremos una clase que represente el concepto de lista.

```java
public class ListaSecuencial {

    private int[] datos;
    private int cantidad;

    public ListaSecuencial(int capacidad) {
        datos = new int[capacidad];
        cantidad = 0;
    }

}
```

Observe que tanto el arreglo como la cantidad de elementos son privados.

Esto evita que otras clases puedan modificarlos directamente.

---

# 10. Agregando operaciones

## Insertar

```java
public boolean insertar(int valor) {

    if (cantidad == datos.length)
        return false;

    datos[cantidad++] = valor;
    return true;

}
```

---

## Obtener tamaño

```java
public int size() {
    return cantidad;
}
```

---

## Obtener elemento

```java
public int obtener(int posicion) {

    if (posicion < 0 || posicion >= cantidad)
        throw new IndexOutOfBoundsException();

    return datos[posicion];

}
```

---

## Buscar

```java
public int buscar(int valor) {

    for (int i = 0; i < cantidad; i++) {

        if (datos[i] == valor)
            return i;

    }

    return -1;

}
```

---

## Eliminar

```java
public boolean eliminar(int posicion) {

    if (posicion < 0 || posicion >= cantidad)
        return false;

    for (int i = posicion; i < cantidad - 1; i++) {
        datos[i] = datos[i + 1];
    }

    cantidad--;

    return true;

}
```

---

# 11. Utilizando la nueva clase

Ahora el programa principal se simplifica considerablemente.

```java
public class Main {

    public static void main(String[] args) {

        ListaSecuencial lista = new ListaSecuencial(10);

        lista.insertar(15);
        lista.insertar(8);
        lista.insertar(32);
        lista.insertar(20);

        for (int i = 0; i < lista.size(); i++) {
            System.out.println(lista.obtener(i));
        }

    }

}
```

Observe que el método `main()` ya no conoce cómo se almacenan los datos.

Únicamente solicita servicios a la lista.

---

# 12. Comparación entre ambos enfoques

| Primer enfoque | Enfoque encapsulado |
|----------------|---------------------|
| El arreglo pertenece al programa principal. | El arreglo pertenece a la clase ListaSecuencial. |
| La variable cantidad también pertenece al programa principal. | La cantidad queda protegida dentro de la clase. |
| El código se repite en diferentes programas. | El código se escribe una sola vez. |
| Difícil mantenimiento. | Fácil mantenimiento. |
| Poco reutilizable. | Altamente reutilizable. |
| Mayor probabilidad de errores. | Menor probabilidad de errores. |

---

# 13. ¿Qué hemos ganado?

Con la clase `ListaSecuencial` obtenemos varias ventajas:

- ocultamos la implementación interna;
- protegemos los datos mediante encapsulamiento;
- reutilizamos la estructura en cualquier programa;
- reducimos la duplicación de código;
- facilitamos el mantenimiento;
- mejoramos la legibilidad;
- hacemos que el programa principal sea mucho más sencillo.

Estas características constituyen precisamente uno de los principales objetivos de la Programación Orientada a Objetos.

---

# 14. Conclusiones

Una lista secuencial no es simplemente un arreglo.

El arreglo representa únicamente el espacio físico donde se almacenan los datos.

La lista secuencial incorpora además las operaciones necesarias para administrar dichos datos de manera controlada.

En una primera etapa resulta útil implementar estas operaciones directamente en el programa principal para comprender el funcionamiento interno de la estructura.

Sin embargo, una vez entendido el algoritmo, la mejor práctica consiste en encapsular toda esa lógica dentro de una clase especializada, como `ListaSecuencial`.

Este cambio permite construir software más organizado, reutilizable y fácil de mantener, además de preparar el camino para estudiar estructuras de datos más complejas como listas enlazadas, pilas, colas y árboles.