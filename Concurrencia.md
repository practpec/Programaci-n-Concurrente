# Principios de la Programación Concurrente

La programación concurrente permite que las computadoras gestionen varias tareas al mismo tiempo o de manera intercalada, optimizando así el uso de los recursos disponibles en el sistema. Esto no significa que las computadoras puedan ejecutar literalmente varias tareas al mismo tiempo, sino que se aprovecha el tiempo de manera más eficiente, lo que es clave para aplicaciones que tienen que manejar procesos largos o independientes, como servidores, bases de datos o incluso aplicaciones móviles.

Lo que más me ha impresionado de este enfoque es cómo mejora el rendimiento de las aplicaciones. Por ejemplo, un servidor que recibe muchas solicitudes de usuarios no puede esperar a que cada tarea termine antes de comenzar otra, de lo contrario sería ineficiente. La programación concurrente ayuda a organizar y ejecutar estas tareas de manera que se aproveche al máximo el tiempo y los recursos del sistema, manteniendo la aplicación rápida y eficiente.

En este artículo, quiero compartir lo que he aprendido sobre la programación concurrente, desde sus principios básicos. A lo largo de este texto, verás ejemplos y explicaciones sencillas que te ayudarán a entender cómo funciona esta técnica y cómo se pueden resolver problemas comunes, como las condiciones de carrera o la sincronización de hilos, para asegurar que las aplicaciones concurrentes funcionen correctamente.


## Tabla de Contenidos

- [Principios de la Programación Concurrente](#principios-de-la-programación-concurrente)
  - [Tabla de Contenidos](#tabla-de-contenidos)
  - [Concurrencia vs Paralelismo](#concurrencia-vs-paralelismo)
  - [Sincronización de Hilos](#sincronización-de-hilos)
    - [Ejemplo:](#ejemplo)
  - [Exclusión Mutua](#exclusión-mutua)
    - [Ejemplo:](#ejemplo-1)
  - [Teoría de los Monitores y Semáforos](#teoría-de-los-monitores-y-semáforos)
    - [Monitores](#monitores)
    - [Semáforos](#semáforos)
      - [Existen dos tipos de semáforos:](#existen-dos-tipos-de-semáforos)
    - [Ejemplo de semáforo:](#ejemplo-de-semáforo)
  - [Problemas Comunes en Programación Concurrente](#problemas-comunes-en-programación-concurrente)
    - [Deadlock (Interbloqueo)](#deadlock-interbloqueo)
      - [Ejemplo de Deadlock](#ejemplo-de-deadlock)
      - [Prevención de Deadlock](#prevención-de-deadlock)
    - [Livelock](#livelock)
      - [Ejemplo de Livelock](#ejemplo-de-livelock)
      - [Prevención de Livelock](#prevención-de-livelock)
    - [Condiciones de Carrera](#condiciones-de-carrera)
      - [Ejemplo de Condición de Carrera](#ejemplo-de-condición-de-carrera)
      - [Prevención de Condiciones de Carrera](#prevención-de-condiciones-de-carrera)
  - [Patrones de Programación Concurrente](#patrones-de-programación-concurrente)
    - [1. Productor-Consumidor](#1-productor-consumidor)
      - [Problema del Productor-Consumidor](#problema-del-productor-consumidor)
      - [Solución:](#solución)
    - [2. Lector-Escritor](#2-lector-escritor)
      - [Problema del Lector-Escritor](#problema-del-lector-escritor)
      - [Solución:](#solución-1)
  - [Conclusión](#conclusión)

## Concurrencia vs Paralelismo

![Concurrente VS Paralela](cvsp.png)

Antes de adentrarnos en los detalles de la programación concurrente, es importante distinguir entre **concurrencia** y **paralelismo**. Aunque estos términos se usan de manera intercambiable, tienen diferencias clave.

- **Concurrencia** se refiere a la capacidad de un sistema para manejar múltiples tareas a la vez, sin necesariamente ejecutarlas simultáneamente. En sistemas concurrentes, las tareas pueden interrumpirse, ceder el control o ser intercaladas por otras, permitiendo que el sistema aproveche mejor los recursos, aunque solo se ejecute una tarea en cada instante.
  
- **Paralelismo**, por otro lado, implica la ejecución simultánea de varias tareas, aprovechando múltiples núcleos o procesadores. El paralelismo se puede lograr cuando las tareas son independientes y pueden ejecutarse en paralelo sin interferencia entre ellas.

El concepto clave en la programación concurrente es que, aunque las tareas no se ejecuten simultáneamente, el sistema debe manejarlas de manera eficiente para aprovechar al máximo los recursos disponibles.

## Sincronización de Hilos

![Sincronización](go.png)


La **sincronización de hilos** es fundamental en la programación concurrente. Se refiere al control de la ejecución de múltiples hilos para evitar que accedan a recursos compartidos de manera desordenada. En un programa concurrente, múltiples hilos pueden acceder a variables o estructuras de datos comunes, lo que puede resultar en un comportamiento inesperado o errores si no se controla adecuadamente.

Los métodos de sincronización permiten que los hilos se coordinen y trabajen en conjunto sin interferir entre sí. Algunos de los mecanismos más comunes de sincronización incluyen:

- **Bloqueo de hilos**: Utilizado para asegurar que un hilo no acceda a un recurso mientras otro lo está utilizando.
- **Condiciones de espera**: Permiten que un hilo se detenga hasta que una condición específica se cumpla.

### Ejemplo:
```plaintext
hilo_1:
    adquirir_bloqueo(recurso)
    realizar_operaciones()
    liberar_bloqueo(recurso)

hilo_2:
    adquirir_bloqueo(recurso)
    realizar_operaciones()
    liberar_bloqueo(recurso)
```

En este ejemplo, los hilos deben adquirir un bloqueo antes de acceder al recurso compartido y liberarlo después de usarlo para evitar conflictos.

## Exclusión Mutua

La **exclusión mutua** es un principio que garantiza que solo un hilo pueda acceder a un recurso compartido en un momento dado. Esto es esencial para evitar problemas como las condiciones de carrera, que ocurren cuando dos o más hilos intentan modificar el mismo recurso al mismo tiempo, lo que puede llevar a resultados impredecibles.

### Ejemplo:
Supongamos que tenemos un contador compartido entre dos hilos. Sin exclusión mutua, ambos hilos podrían intentar incrementar el contador al mismo tiempo, resultando en un valor incorrecto.

Para evitarlo, se usa exclusión mutua, de manera que solo un hilo pueda incrementar el contador a la vez:

```plaintext
hilo_1:
    adquirir_bloqueo(contador)
    contador = contador + 1
    liberar_bloqueo(contador)

hilo_2:
    adquirir_bloqueo(contador)
    contador = contador + 1
    liberar_bloqueo(contador)
```


## Teoría de los Monitores y Semáforos

### Monitores

Un **monitor** es un mecanismo de sincronización que permite que solo un hilo acceda a un recurso a la vez. En su implementación, los monitores encapsulan tanto el recurso compartido como las operaciones de sincronización necesarias, proporcionando un nivel más alto de abstracción.

Los monitores tienen dos componentes principales:

- **Variables de condición**: Permiten que los hilos esperen hasta que se cumpla una condición.
- **Métodos**: Son las operaciones que los hilos pueden ejecutar dentro del monitor.

Los monitores permiten escribir código concurrente de manera más clara y menos propensa a errores que otros mecanismos de sincronización como los bloqueos manuales.

### Semáforos

Un **semáforo** es una variable o estructura de datos que controla el acceso a un recurso compartido mediante un contador. Los semáforos permiten coordinar múltiples hilos y asegurar que no excedan el número máximo de hilos que pueden acceder a un recurso al mismo tiempo.

#### Existen dos tipos de semáforos:

- **Semáforos binarios (o mutex)**: Tienen un contador que solo puede ser 0 o 1, lo que garantiza la exclusión mutua.
- **Semáforos contadores**: Permiten que un número específico de hilos accedan a un recurso.

### Ejemplo de semáforo:
Supongamos que tenemos un semáforo con un valor de 1, que solo permite que un hilo acceda a un recurso compartido.

```plaintext
semáforo = 1

hilo_1:
    esperar(semáforo)
    realizar_operaciones()
    señalizar(semáforo)

hilo_2:
    esperar(semáforo)
    realizar_operaciones()
    señalizar(semáforo)
```

En este ejemplo, `esperar()` disminuye el valor del semáforo, y `señalizar()` lo incrementa, permitiendo que un hilo pase a la siguiente operación solo si el semáforo lo permite.

Aquí tienes el contenido en Markdown con una sección adicional sobre los problemas comunes en programación concurrente, como *deadlock*, *race conditions* y *livelock*:

## Problemas Comunes en Programación Concurrente

La programación concurrente, aunque poderosa y eficiente, trae consigo una serie de problemas que deben ser manejados adecuadamente para evitar errores y garantizar la correcta ejecución de los programas. Entre los problemas más comunes se encuentran el **deadlock**, el **livelock** y las **condiciones de carrera**. A continuación, exploramos cada uno de estos problemas.

### Deadlock (Interbloqueo)

El **deadlock** ocurre cuando dos o más hilos se bloquean mutuamente porque cada uno está esperando que el otro libere un recurso. Este es uno de los problemas más críticos en sistemas concurrentes y puede llevar a una detención indefinida del sistema. Un deadlock puede suceder en situaciones como las siguientes:

- Hilo A tiene el recurso 1 y espera el recurso 2.
- Hilo B tiene el recurso 2 y espera el recurso 1.

Dado que cada hilo está esperando el recurso que tiene el otro, ninguno de los dos puede continuar, resultando en un **deadlock**.

#### Ejemplo de Deadlock

```plaintext
hilo_1:
    adquirir_bloqueo(recurso_1)
    esperar(recurso_2)
    liberar_bloqueo(recurso_1)

hilo_2:
    adquirir_bloqueo(recurso_2)
    esperar(recurso_1)
    liberar_bloqueo(recurso_2)
```

En este ejemplo, ambos hilos adquieren un recurso y luego esperan por el recurso que el otro posee, lo que genera un deadlock.

#### Prevención de Deadlock
Para evitar los deadlocks, se pueden aplicar estrategias como:
- **Evitar la espera circular**: Asegurarse de que no existan ciclos de dependencia en los recursos.
- **Asignación de recursos con prioridad**: Siempre adquirir recursos en el mismo orden.

### Livelock

Un **livelock** es similar a un deadlock, pero en lugar de detenerse completamente, los hilos están en constante movimiento, realizando operaciones sin progreso. Es decir, los hilos están "vivos", pero siguen repitiendo las mismas acciones sin lograr completar su tarea.

Un livelock puede ocurrir cuando los hilos siguen reaccionando a los eventos que suceden entre sí, sin llegar a una resolución de sus tareas.

#### Ejemplo de Livelock

```plaintext
hilo_1:
    adquirir_bloqueo(recurso)
    liberar_bloqueo(recurso)
    intentar_otra_vez()

hilo_2:
    adquirir_bloqueo(recurso)
    liberar_bloqueo(recurso)
    intentar_otra_vez()
```

En este ejemplo, ambos hilos intentan adquirir un recurso, pero debido a que continuamente se liberan y reintentan el bloqueo, nunca logran hacer su trabajo.

#### Prevención de Livelock
Para evitar los livelocks, se debe:
- Introducir condiciones de espera apropiadas para permitir que los hilos avancen sin necesidad de estar continuamente intentando el mismo recurso.

### Condiciones de Carrera

Las **condiciones de carrera** ocurren cuando dos o más hilos intentan acceder y modificar un recurso compartido al mismo tiempo, y el resultado final depende del orden en que se ejecutan las operaciones. Si no se manejan adecuadamente, las condiciones de carrera pueden causar resultados impredecibles o inconsistentes.

#### Ejemplo de Condición de Carrera

```plaintext
hilo_1:
    contador = contador + 1
    imprimir(contador)

hilo_2:
    contador = contador + 1
    imprimir(contador)
```

En este caso, si ambos hilos leen el valor de `contador` al mismo tiempo, pueden escribir el mismo valor, lo que provoca un resultado incorrecto.

#### Prevención de Condiciones de Carrera
Las condiciones de carrera se pueden evitar utilizando mecanismos de sincronización como:
- **Bloqueos**: Garantizar que un solo hilo acceda al recurso compartido a la vez.
- **Semáforos**: Controlar el acceso de los hilos a los recursos compartidos.


## Patrones de Programación Concurrente

En la programación concurrente, existen varios patrones utilizados para gestionar la ejecución de múltiples hilos que deben acceder a recursos compartidos o realizar tareas específicas de manera eficiente. Dos de los patrones más conocidos son el **Productor-Consumidor** y el **Lector-Escritor**. A continuación, vamos a explorar estos patrones y sus respectivos problemas y soluciones.

### 1. Productor-Consumidor

El patrón **Productor-Consumidor** es un ejemplo clásico de programación concurrente en el que existen dos tipos de hilos: los productores y los consumidores. Los productores generan datos y los colocan en un buffer o cola, mientras que los consumidores toman estos datos para procesarlos. El principal desafío aquí es coordinar a los hilos para evitar que el productor agregue datos cuando el buffer esté lleno o que el consumidor intente acceder al buffer vacío.

![Concurrente VS Paralela](procon.jpg)

#### Problema del Productor-Consumidor

El problema radica en asegurar que los hilos productores no sobrecarguen el buffer y que los consumidores no intenten acceder a datos cuando no hay ninguno disponible. Para manejar esto, se utilizan mecanismos de sincronización como semáforos y variables de condición.

#### Solución:


```plaintext
semáforo vacio = tamaño del buffer
semáforo lleno = 0
mutex = 1  // Para sincronizar el acceso al buffer

// Hilo Productor
Productor:
    while(true):
        item = producir_dato()
        esperar(vacio)
        adquirir_bloqueo(mutex)
        colocar_en_buffer(item)
        liberar_bloqueo(mutex)
        señalizar(lleno)

// Hilo Consumidor
Consumidor:
    while(true):
        esperar(lleno)
        adquirir_bloqueo(mutex)
        item = sacar_del_buffer()
        liberar_bloqueo(mutex)
        señalizar(vacio)
        consumir_dato(item)
```


- **vacio** y **lleno** son semáforos que mantienen el control sobre el buffer. El semáforo **vacio** cuenta el espacio disponible en el buffer, mientras que **lleno** lleva cuenta de los elementos producidos pero no consumidos.
- **mutex** se usa para asegurar que solo un hilo acceda al buffer a la vez.

### 2. Lector-Escritor

El patrón **Lector-Escritor** es otro patrón común en programación concurrente, donde múltiples hilos pueden leer de un recurso compartido de manera simultánea, pero solo un hilo puede escribir en el recurso a la vez. El reto de este patrón es equilibrar la lectura concurrente con la exclusión mutua necesaria para las escrituras, evitando que los lectores y escritores se bloqueen entre sí de manera innecesaria.
![Concurrente VS Paralela](lectesc.jpg)

#### Problema del Lector-Escritor

El problema básico es garantizar que si un hilo está escribiendo, ningún otro hilo (ni lector ni otro escritor) puede acceder al recurso. Además, se debe permitir que varios hilos lectores accedan al recurso simultáneamente, siempre y cuando no haya un escritor activo. Para resolver esto, se utilizan semáforos o monitores que aseguren que se respete la exclusión mutua entre escritores y la concurrencia de lectores.

#### Solución:

```plaintext
semáforo mutex = 1  // Para sincronizar el acceso a la variable de cuenta
semáforo escritor = 1  // Para controlar el acceso exclusivo del escritor
int contador_lectores = 0

// Hilo Lector
Lector:
    while(true):
        adquirir_bloqueo(mutex)
        contador_lectores++
        if contador_lectores == 1:
            esperar(escritor)  // El primer lector bloquea a los escritores
        liberar_bloqueo(mutex)
        
        // Leer el recurso
        leer_recurso()

        adquirir_bloqueo(mutex)
        contador_lectores--
        if contador_lectores == 0:
            señalizar(escritor)  // El último lector desbloquea a los escritores
        liberar_bloqueo(mutex)

// Hilo Escritor
Escritor:
    while(true):
        esperar(escritor)  // Espera a que no haya lectores ni otro escritor
        // Escribir en el recurso
        escribir_recurso()
        señalizar(escritor)  // Libera el acceso al escritor
```


- **mutex** se usa para proteger la variable **contador_lectores**, que lleva la cuenta de cuántos lectores están accediendo al recurso.
- **escritor** es un semáforo utilizado para garantizar que solo un escritor tenga acceso exclusivo al recurso.
- Los lectores pueden acceder al recurso simultáneamente, pero el primer lector bloquea a los escritores, y el último lector desbloquea el acceso para que un escritor pueda operar sin interferencias.


Estos dos patrones son fundamentales en la programación concurrente y, aunque puedan parecer complicados al principio, entender su funcionamiento y cómo se pueden resolver problemas de sincronización es clave para diseñar aplicaciones eficientes y seguras.

## Conclusión

Al llegar al final de este artículo, podemos ver lo fascinante y relevante que es el tema de la **programación concurrente**. Aunque a primera vista puede parecer complejo, tiene un impacto fundamental en la creación de aplicaciones eficientes y de alto rendimiento. La capacidad de ejecutar múltiples tareas simultáneamente, sin que interfieran entre sí, es esencial para aprovechar al máximo los recursos de un sistema moderno. A través de la concurrencia, podemos hacer que nuestras aplicaciones sean más rápidas, escalables y optimizadas.

Un aspecto crucial que aprendí durante el estudio de este tema fue la distinción entre **concurrencia** y **paralelismo**. Si bien ambos conceptos están relacionados, no son lo mismo. La **concurrencia** se refiere a la capacidad de un sistema para gestionar varias tareas a la vez, aunque no necesariamente de manera simultánea. En cambio, el **paralelismo** implica ejecutar múltiples tareas al mismo tiempo, aprovechando múltiples núcleos o procesadores. Es fundamental comprender estas diferencias, ya que definen cómo un sistema puede abordar y distribuir tareas de manera eficiente.

Uno de los desafíos más importantes al trabajar con programación concurrente es la **sincronización de los hilos**. Sin una correcta sincronización, los hilos pueden entrar en conflicto al acceder a recursos compartidos, lo que genera **condiciones de carrera**. Este tipo de errores ocurren cuando dos o más hilos intentan modificar un recurso al mismo tiempo sin control adecuado. Afortunadamente, herramientas como **semáforos**, **monitores** y **bloqueos** permiten gestionar este acceso, asegurando que los hilos trabajen de forma coordinada y sin interferencias.

Otro concepto relevante en la programación concurrente es la **exclusión mutua**, que previene errores graves al garantizar que solo un hilo pueda modificar un recurso a la vez. Por ejemplo, si varios hilos intentan modificar un contador compartido sin sincronización, los resultados finales pueden ser incorrectos. Mediante el uso de bloqueos o mecanismos similares, podemos asegurarnos de que solo un hilo acceda al recurso en un momento determinado, evitando resultados inconsistentes.

Tambien cabe destacar que existen problemas complejos que pueden surgir en la programación concurrente, como el **deadlock** (interbloqueo) y el **livelock**. El **deadlock** ocurre cuando dos o más hilos quedan bloqueados esperando que el otro libere un recurso, lo que lleva a que ninguno de los hilos pueda continuar. El **livelock**, por su parte, sucede cuando los hilos siguen ejecutándose pero nunca logran avanzar, atrapados en un ciclo constante de espera. Estos problemas pueden parecer intimidantes, pero con las prácticas adecuadas, como evitar ciclos de espera o asegurar condiciones de desbloqueo claras, es posible prevenirlos.

En cuanto a los patrones de programación concurrente, existen varios muy utiles en este articulo se mencionan dos: el **Productor-Consumidor** y el **Lector-Escritor**. El primero maneja situaciones en las que un hilo produce datos y otro los consume. Aquí, es vital que el productor no genere más datos de los que el consumidor puede procesar. En el patrón Lector-Escritor, múltiples hilos pueden leer simultáneamente un recurso, pero solo uno puede escribir en él a la vez para evitar la corrupción de los datos. Estos patrones son solo algunos ejemplos de cómo la programación concurrente organiza y facilita la interacción entre hilos en escenarios de recursos compartidos.

Aunque la programación concurrente puede parecer desafiante al principio, los beneficios que ofrece, tanto en términos de eficiencia como de rendimiento, son invaluables. En cuanto se entiende bien cómo coordinar y sincronizar los hilos, es posible crear aplicaciones mucho más rápidas, eficientes y escalables.

Para ilustrar la aplicación de estos conceptos, quiero compartir algunos proyectos en los que he trabajado, los cuales son ejemplos claros de programación concurrente en acción.

Uno de estos proyectos es un **juego concurrente** desarrollado con **Phaser.js** y **HTML**. En este juego, se utilizan **hilos** para manejar varias tareas simultáneamente, como la lógica del juego, la animación y la actualización de la interfaz. Puedes revisar el código y los detalles del proyecto en este [repositorio de GitHub](https://github.com/practpec/juego_concurrente.git). Este es un ejemplo claro de cómo podemos utilizar la concurrencia para mejorar la experiencia de usuario, haciendo que el juego sea más fluido y con múltiples aspectos corriendo en paralelo.

Otro proyecto interesante es un **simulador de estacionamiento concurrente**, desarrollado con **Go** y **Fyne**. En este simulador, utilizamos **semáforos** para controlar el acceso al estacionamiento, determinando qué autos pueden entrar o salir de acuerdo a la disponibilidad de espacios. Este ejemplo demuestra cómo la sincronización de hilos es clave cuando tenemos recursos limitados que deben ser gestionados de forma eficiente. Puedes ver más sobre este proyecto en este [repositorio de GitHub](https://github.com/practpec/parking_go.git).

Con estos proyectos, se pueden ver de forma práctica cómo se aplican los principios de la programación concurrente en escenarios reales. Ambos son ejemplos de cómo, al manejar múltiples hilos y recursos compartidos, podemos crear aplicaciones más dinámicas y con un rendimiento mejorado.

Si este artículo te ha ayudado a comprender los conceptos clave de la programación concurrente y cómo implementarlos en tus propios proyectos, me sentiré muy satisfecho. Gracias por haber llegado hasta aquí y espero que esta información te haya sido útil.

