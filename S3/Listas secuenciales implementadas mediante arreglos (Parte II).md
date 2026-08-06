# Listas secuenciales implementadas mediante arreglos (Parte II)

**Programación III – Ingeniería Multimedia**  
**Tema:** Generalización de una lista secuencial mediante clases genéricas en Java

---

# 1. Introducción

En el documento anterior construimos una clase denominada `ListaSecuencial`, capaz de administrar una colección de números enteros utilizando únicamente un arreglo simple.

Aunque dicha implementación resolvía correctamente el problema, presentaba una limitación importante: **solamente podía almacenar valores de tipo `int`.**

Pero...

¿Qué ocurriría si deseáramos almacenar nombres de personas?

¿O estudiantes?

¿O imágenes?

¿O cualquier otro tipo de objeto?

Una primera solución podría consistir en crear una nueva clase para cada tipo de dato.

Por ejemplo:

```text
ListaEnteros
ListaString
ListaEstudiantes
ListaImagenes
ListaVehiculos
ListaProductos
...
```

Claramente esta no es una buena solución.

Todas esas clases realizarían exactamente las mismas operaciones:

- insertar
- eliminar
- buscar
- obtener
- reemplazar
- recorrer

Lo único que cambiaría sería el tipo de dato almacenado.

Desde el punto de vista del diseño de software estaríamos duplicando código innecesariamente.

---

# 2. La reutilización como principio de diseño

Uno de los objetivos más importantes de la Programación Orientada a Objetos consiste en escribir código reutilizable.

Una buena pregunta que un programador debe hacerse constantemente es:

> **¿Estoy resolviendo el mismo problema varias veces?**

Si la respuesta es sí, probablemente exista una mejor solución.

En nuestro caso, el algoritmo de inserción siempre es exactamente el mismo.

Por ejemplo:

```java
datos[cantidad++] = elemento;
```

No importa si `elemento` representa:

- un entero;
- un estudiante;
- una imagen;
- un vehículo;
- un videojuego.

El procedimiento de inserción no cambia.

Entonces...

¿Por qué escribir varias clases?

---

# 3. Las clases genéricas

Java permite indicar que una clase trabajará con un **tipo de dato aún desconocido**.

Ese tipo se representa mediante una letra.

Generalmente se utiliza la letra:

```text
T
```

que significa **Type**.

Por ejemplo:

```java
public class ListaSecuencial<T> {

}
```

La letra `T` **no representa un tipo específico**.

Representa un tipo cualquiera que será definido cuando se cree un objeto.

---

# 4. Una analogía

Imagine una caja vacía.

La caja no sabe qué contendrá.

Puede almacenar:

- libros;
- celulares;
- cámaras;
- cuadernos.

La caja siempre es la misma.

Únicamente cambia aquello que el usuario decide guardar.

Una clase genérica funciona exactamente igual.

La clase conoce cómo administrar los elementos, pero no necesita conocer qué tipo de elemento será.

---

# 5. Transformando nuestra clase

En la primera versión teníamos:

```java
private int[] datos;
```

Ahora necesitamos reemplazar el tipo entero por un tipo genérico.

Sin embargo, aparece un pequeño inconveniente.

Java no permite crear directamente un arreglo genérico mediante:

```java
new T[10]
```

porque el tipo real todavía no existe durante la compilación.

La solución más utilizada consiste en crear un arreglo de objetos y convertirlo al tipo genérico.

```java
@SuppressWarnings("unchecked")
public ListaSecuencial(int capacidad){

    datos = (T[]) new Object[capacidad];
    cantidad = 0;

}
```

El atributo ahora queda definido como

```java
private T[] datos;
```

Observe que la única diferencia consiste en reemplazar `int` por `T`.

---

# 6. La nueva definición de la clase

```java
public class ListaSecuencial<T> {

    private T[] datos;
    private int cantidad;

    @SuppressWarnings("unchecked")
    public ListaSecuencial(int capacidad){

        datos = (T[]) new Object[capacidad];
        cantidad = 0;

    }

}
```

Observe que prácticamente toda la estructura permanece igual.

Esto demuestra que el algoritmo de administración de la lista es independiente del tipo de dato almacenado.

---

# 7. Adaptando los métodos

## Insertar

Antes:

```java
public boolean insertar(int valor)
```

Ahora:

```java
public boolean insertar(T valor){

    if(cantidad == datos.length)
        return false;

    datos[cantidad++] = valor;

    return true;

}
```

Observe que únicamente cambió el tipo del parámetro.

---

## Obtener

```java
public T obtener(int posicion){

    if(posicion < 0 || posicion >= cantidad)
        throw new IndexOutOfBoundsException();

    return datos[posicion];

}
```

El método ya no devuelve un entero.

Devuelve un objeto del tipo indicado por el usuario.

---

## Reemplazar

```java
public boolean reemplazar(int posicion, T nuevoValor){

    if(posicion < 0 || posicion >= cantidad)
        return false;

    datos[posicion] = nuevoValor;

    return true;

}
```

---

## Tamaño

```java
public int size(){

    return cantidad;

}
```

Observe que este método no cambia, ya que el tamaño de una lista siempre es un entero.

---

# 8. ¿Qué ocurre con la búsqueda?

En la versión para enteros utilizábamos:

```java
if(datos[i] == valor)
```

Con objetos esto deja de ser correcto.

¿Por qué?

Suponga el siguiente caso:

```java
String a = new String("Hola");
String b = new String("Hola");
```

Visualmente contienen el mismo texto.

Sin embargo:

```java
a == b
```

produce

```text
false
```

porque ambas variables hacen referencia a objetos diferentes.

Para comparar objetos debemos utilizar:

```java
equals()
```

Entonces la búsqueda queda:

```java
public int buscar(T elemento){

    for(int i=0;i<cantidad;i++){

        if(datos[i].equals(elemento))
            return i;

    }

    return -1;

}
```

Este pequeño cambio es uno de los aspectos más importantes al trabajar con clases genéricas.

---

# 9. Utilizando la lista con enteros

```java
ListaSecuencial<Integer> numeros =
        new ListaSecuencial<>(20);

numeros.insertar(15);
numeros.insertar(8);
numeros.insertar(30);
```

Observe que ya no utilizamos `int`.

Utilizamos la clase envolvente (**Wrapper Class**) `Integer`.

---

# 10. Utilizando la lista con cadenas

```java
ListaSecuencial<String> nombres =
        new ListaSecuencial<>(10);

nombres.insertar("Ana");
nombres.insertar("Carlos");
nombres.insertar("Laura");
```

La misma clase administra ahora textos.

No fue necesario modificar absolutamente nada.

---

# 11. Utilizando la lista con objetos propios

Supongamos la siguiente clase.

```java
public class Estudiante{

    private String codigo;
    private String nombre;

    public Estudiante(String codigo, String nombre){

        this.codigo = codigo;
        this.nombre = nombre;

    }

}
```

Ahora la lista puede almacenar estudiantes.

```java
ListaSecuencial<Estudiante> estudiantes =
        new ListaSecuencial<>(50);

estudiantes.insertar(
        new Estudiante("20261001","Laura"));

estudiantes.insertar(
        new Estudiante("20261002","Carlos"));
```

Observe que nuevamente la clase `ListaSecuencial` no necesitó modificarse.

---

# 12. Una ventaja enorme

Con una sola clase podemos construir listas de cualquier tipo.

```text
ListaSecuencial<Integer>

ListaSecuencial<Double>

ListaSecuencial<Character>

ListaSecuencial<String>

ListaSecuencial<Estudiante>

ListaSecuencial<Imagen>

ListaSecuencial<Video>

ListaSecuencial<Audio>
```

Todas utilizan exactamente el mismo código.

---

# 13. Diagrama conceptual

```text
                 ListaSecuencial<T>

              +----------------------+
              | datos[]              |
              | cantidad             |
              +----------------------+
              | insertar()           |
              | eliminar()           |
              | buscar()             |
              | obtener()            |
              | reemplazar()         |
              +----------------------+

                       ▲
                       │
        -------------------------------------
        │              │            │
        │              │            │
   <Integer>      <String>    <Estudiante>
```

La clase existe una sola vez.

Los tipos concretos se definen únicamente al crear el objeto.

---

# 14. Autoboxing y Wrapper Classes

En este punto surge una pregunta frecuente.

¿Por qué escribimos?

```java
ListaSecuencial<Integer>
```

y no

```java
ListaSecuencial<int>
```

La respuesta es sencilla.

Los tipos genéricos de Java únicamente aceptan **objetos**.

Los tipos primitivos (`int`, `double`, `char`, `boolean`, etc.) no son objetos.

Por ello Java proporciona clases equivalentes denominadas **Wrapper Classes**.

| Tipo primitivo | Clase envolvente |
|----------------|------------------|
| int | Integer |
| double | Double |
| float | Float |
| long | Long |
| short | Short |
| byte | Byte |
| char | Character |
| boolean | Boolean |

Afortunadamente, Java realiza automáticamente la conversión entre ambos tipos.

Por ejemplo:

```java
numeros.insertar(25);
```

Aunque `25` es un `int`, Java lo convierte automáticamente en un objeto `Integer`.

Este proceso recibe el nombre de **Autoboxing**.

De forma similar, cuando recuperamos un elemento:

```java
int x = numeros.obtener(0);
```

Java transforma automáticamente el objeto `Integer` en un valor `int`.

Este proceso se denomina **Unboxing**.

---

# 15. Buenas prácticas

Al diseñar una estructura de datos reutilizable es recomendable que:

- todos los atributos sean privados;
- el acceso a los datos se realice mediante métodos;
- la implementación permanezca oculta al usuario;
- las validaciones se realicen dentro de la propia estructura;
- la clase no dependa de un tipo de dato específico.

Estas características favorecen la reutilización y disminuyen la probabilidad de errores.

---

# 16. Conclusiones

Las clases genéricas representan uno de los mecanismos más poderosos de Java para construir software reutilizable.

Al reemplazar un tipo específico por un parámetro genérico (`T`), logramos que una misma implementación pueda administrar objetos de cualquier naturaleza sin duplicar código.

Este enfoque constituye la base de la mayoría de las colecciones del Framework de Java, como `ArrayList`, `LinkedList`, `HashSet` y `HashMap`. Comprender cómo construir una estructura genérica desde cero permite entender con mayor profundidad el funcionamiento interno de dichas colecciones y prepara al estudiante para diseñar sus propias estructuras de datos reutilizables.

En la siguiente unidad se aprovechará esta implementación para incorporar **iteradores**, **listas ordenadas**, **criterios de comparación mediante `Comparable<T>` y `Comparator<T>`**, así como el análisis del costo computacional de cada operación utilizando la notación Big-O.