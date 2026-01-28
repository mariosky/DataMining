Los Datos
=========

La materia prima para realizar nuestra labor de mineros de datos es inagotable: los
datos, ese recurso en bruto que debemos procesar para sacarle provecho. En esta sección
discutiremos temas relacionados con los conjuntos de datos y el tipo de operaciones que
podemos hacer con ellos.

Las tareas de esta sección serán:

- Definir a los datos como objetos.
- Conocer tipos de estructuras para conjuntos de datos:

  - Tablas
  - Matrices
  - Grafos

Datos y objetos
---------------

Como la minería de datos es una actividad multidisciplinaria, muchas veces hay confusión
(o incluso diferencias ideológicas) con respecto a los nombres y términos que se utilizan.
Si a esto le agregamos la traducción al castellano, podríamos pasarnos mucho tiempo
discutiendo sin llegar a una definición única.

Por ejemplo, en inglés se le llama *data* al plural de *datum* (dato), lo cual al principio
puede confundir a quienes hablamos español.

.. note::

   Pero no a quienes sepan que *data* es el plural neutro para un sustantivo de la primera
   declinación en latín. Véase: https://es.wiktionary.org/wiki/datus

Como primer paso definiremos conceptos básicos, usando (en lo posible) términos familiares
para profesionistas de informática.

**Datos**
   Representación de observaciones recolectadas. Por ejemplo, un médico puede medir la
   temperatura corporal de varios pacientes y registrar la lectura en grados centígrados.

**Objetos**
   Normalmente representamos cada observación como un objeto (o registro). Por ejemplo, una
   lectura de signos vitales podría representarse como el siguiente objeto:

   .. list-table::
      :header-rows: 1
      :widths: 30 20

      * - Atributo
        - Valor
      * - id
        - 1
      * - nombre
        - Juan Pérez
      * - día
        - Lunes
      * - estatura
        - 175
      * - temperatura
        - 36.2
      * - presión_diastólica
        - 80
      * - presión_sistólica
        - 135
      * - frecuencia_cardíaca
        - 90

**Atributos**
   Propiedades o características de los objetos de cierto tipo. Todas las observaciones
   del mismo tipo de objeto tendrán los mismos atributos. Para el ejemplo anterior:

   ``{id, nombre, día, estatura, temperatura, presión_diastólica, presión_sistólica, frecuencia_cardíaca}``

**Valores**
   El *estado* de un objeto corresponde a los valores que toman sus atributos en un momento
   dado. En análisis de datos solemos trabajar con datos históricos, por lo que rara vez se
   modelan cambios de estado dentro del mismo registro.

   Los valores, a su vez, también se pueden considerar como objetos. Por ejemplo, el atributo
   ``nombre`` toma como valor una cadena: ``"Juan Pérez"``.

   Para analizar observaciones de forma estadística debemos considerar el tipo de dato:

   - **Dato cualitativo.** La observación se expresa en palabras. Por ejemplo, una reseña de un
     restaurante. Este tipo de datos puede ayudarnos a etiquetar objetos como miembros de una
     clase o grupo. En el caso de un restaurante, un atributo como ``tipo_de_cocina`` puede
     tomar valores como *mexicana*, *japonesa* o *italiana*.

   - **Dato cuantitativo.** La observación corresponde a una magnitud. Se suele dividir en:

     - **Discreto.** Enteros, normalmente conteos. Ejemplos: *número de empleados*, *visitas*,
       *número de páginas*, *temporada*.
     - **Continuo.** Mediciones en números reales, como *temperatura corporal*, *sueldo* o
       *duración*.

**Conjunto de datos**
   Colecciones de objetos (casi siempre del mismo tipo). Es común representarlos como registros
   en una tabla. Por ejemplo, un conjunto de lecturas de signos vitales:

   .. list-table::
      :header-rows: 1
      :widths: 8 22 16 14 14

      * - id
        - nombre
        - temperatura
        - día
        - estatura
      * - 1
        - Juan Pérez
        - 36.2
        - Lunes
        - 175
      * - 2
        - Ana Rivas
        - 136.3
        - Martes
        - 125
      * - 3
        - María Sánchez
        - 36.5
        - Jueves
        - 190
      * - 4
        - Luis Duarte
        - 39.0
        - Lunes
        - 180
      * - 5
        - José Arias
        - 36.5
        - Viernes
        - 90
      * - 6
        - Margarita Lee
        - 39.0
        - Jueves
        - 173

A los conjuntos de datos cuando no se han procesado les llamamos **datos crudos** (*raw data*).
En el ejemplo anterior, por ejemplo, hay valores erróneos: la temperatura del paciente con

.. math::

   id = 2

no puede ser de 136.3 °C. En situaciones reales también puede ser necesario anonimizar los
datos, ya que podrían ser confidenciales.

Clasificación de escalas de medición
------------------------------------

El análisis que podemos realizar en un conjunto de datos depende del tipo de variable de
sus atributos.

Por ejemplo, en el conjunto de pacientes podríamos calcular la media de ``estatura``, pero
no tendría sentido calcular la media de ``nombre``. Incluso con enteros, la media puede no
tener interpretación; por ejemplo, la media del atributo ``id`` no nos dice nada, ya que es
solo una etiqueta.

Utilizaremos la clasificación de Stevens para clasificar los valores asignados a los
atributos en cuatro niveles.

Nominal o categórica
~~~~~~~~~~~~~~~~~~~~

Medición cualitativa que sirve para asignar objetos a un grupo o categoría. Por ejemplo,
el atributo ``especie`` de las flores Iris puede tomar los valores: *setosa*, *virginica* o
*versicolor*.

Una medida categórica no siempre es texto: un número de serie o un número de teléfono también
pueden funcionar como categorías. En algunos casos brindan información cualitativa, como
*país de origen*, *sexo* o *estado civil*.

- Operadores: :math:`=`, :math:`\neq`.
- Medidas y pruebas: moda, entropía, prueba :math:`\chi^2`.
- Operaciones típicas: agrupar.

Ordinal
~~~~~~~

Medición categórica que además tiene un orden. Por ejemplo: niveles de dificultad (*fácil*,
*medio*, *difícil*), talla de ropa, nivel máximo de estudios. Otro ejemplo: una escala de agrado
(*nada*, *poco*, *regular*, *mucho*).

- Operadores: :math:`=`, :math:`\neq`, :math:`>`, :math:`<`.
- Medidas y pruebas: moda, mediana, percentiles, pruebas de signos.
- Operaciones típicas: agrupar y ordenar.

Intervalar
~~~~~~~~~~

Permite establecer diferencias entre valores, pero **no** permite proporciones porque no existe
un cero absoluto. El ejemplo clásico es la temperatura en grados centígrados: la diferencia
entre 3 °C y 5 °C es de 2 °C, pero no podemos decir que 20 °C sea “el doble” de 10 °C.

- Operadores: :math:`=`, :math:`\neq`, :math:`>`, :math:`<`, :math:`+`, :math:`-`.
- Medidas: moda, mediana, media aritmética; rango y desviación estándar.
- Operaciones típicas: agrupar, ordenar y calcular diferencias.

Razón
~~~~~

Cuenta con un cero absoluto, por lo que las proporciones sí son válidas. La mayoría de
mediciones en ingeniería o física usan esta escala: masa, longitud, duración, carga eléctrica,
corriente eléctrica, temperatura en Kelvin, etc.

- Operadores: :math:`=`, :math:`\neq`, :math:`>`, :math:`<`, :math:`+`, :math:`-`,
  :math:`\times`, :math:`\div`.
- Medidas: moda, mediana, media aritmética, media geométrica y media armónica; rango y
  desviación estándar.
- Operaciones típicas: agrupar, ordenar, calcular diferencias y proporciones.

Tipos de conjuntos de datos
---------------------------

Quienes ya tienen experiencia con bases de datos a veces creen (equivocadamente) que la minería
de datos es una extensión de SQL. En realidad, la mayoría de los algoritmos de minería de datos
se ejecutan fuera del servidor, usando lenguajes de propósito general como Python o Java.

Muchos algoritmos provienen de áreas como álgebra lineal, inferencia estadística, inteligencia
artificial y reconocimiento de patrones. En esos campos es común trabajar con **vectores** y
**matrices**, ya que permiten expresar operaciones de manera eficiente.

Como este material es introductorio, nos enfocaremos en tres representaciones básicas:

- tablas,
- matrices,
- grafos.

Tablas
~~~~~~

Recordemos que los primeros pasos del proceso de KDD suelen consistir en llevar los datos desde
su fuente original a un formato adecuado para los algoritmos. El formato de entrada para muchos
modelos es un arreglo de vectores de características, que podemos representar como una tabla.

En algunas herramientas de minería de datos (por ejemplo, Orange o RapidMiner) se especifican
*metadatos* para cada atributo: tipo de atributo (nominal/categórico, continuo, discreto, tiempo,
cadena) y rol (característica, etiqueta/clase, ponderación, ignorar).

Por ejemplo, para el conjunto de datos Iris:

.. list-table::
   :header-rows: 1
   :widths: 15 15 15 15 15

   * - sepal_length
     - sepal_width
     - petal_length
     - petal_width
     - species
   * - 5.1
     - 3.5
     - 1.4
     - 0.2
     - setosa
   * - 4.9
     - 3.0
     - 1.4
     - 0.2
     - setosa
   * - 7.0
     - 3.2
     - 4.7
     - 1.4
     - versicolor
   * - 6.4
     - 3.2
     - 4.5
     - 1.5
     - versicolor
   * - 6.3
     - 3.3
     - 6.0
     - 2.5
     - virginica
   * - 5.8
     - 2.7
     - 5.1
     - 1.9
     - virginica

En Orange, por ejemplo, los metadatos pueden verse así:

.. list-table::
   :header-rows: 1
   :widths: 18 18 18 28

   * - atributo
     - tipo
     - rol
     - meta
   * - sepal_length
     - continuo
     - característica
     -
   * - sepal_width
     - continuo
     - característica
     -
   * - petal_length
     - continuo
     - característica
     -
   * - petal_width
     - continuo
     - característica
     -
   * - species
     - nominal
     - etiqueta
     - setosa, versicolor, virginica

En otros casos, como con `scikit-learn <https://scikit-learn.org/>`_, no se especifican metadatos
de forma explícita; se espera que entreguemos los datos en arreglos separados: uno para
características y otro para etiquetas.

Características:

.. code-block:: python

   [
     [5.1, 3.5, 1.4, 0.2],  # setosa
     [4.9, 3.0, 1.4, 0.2],  # setosa
     [7.0, 3.2, 4.7, 1.4],  # versicolor
     [6.4, 3.2, 4.5, 1.5],  # versicolor
     [6.3, 3.3, 6.0, 2.5],  # virginica
     [5.8, 2.7, 5.1, 1.9],  # virginica
   ]

Etiquetas:

.. code-block:: python

   ["setosa", "setosa", "versicolor", "versicolor", "virginica", "virginica"]

Aunque son dos arreglos distintos, representan el mismo conjunto de datos; por eso es
crítico que las posiciones correspondan (la fila *i* de ``X`` debe corresponder a la
etiqueta *i* de ``y``).

Independientemente de la representación, es muy importante conocer el **tipo** y el **rol**
de cada atributo. Por ejemplo, en Iris todas las características son continuas, mientras que
la etiqueta es categórica. Esto limita qué algoritmos pueden aplicarse directamente.

Más adelante, en la sección de preprocesamiento, veremos técnicas para transformar atributos
y así poder aplicar una mayor variedad de algoritmos y, en algunos casos, mejorar desempeño.

Matrices
~~~~~~~~

A diferencia de las tablas, en una matriz las columnas no representan “atributos” de un objeto.
Más bien, las matrices suelen representar **relaciones entre objetos**.

Por ejemplo, una matriz puede representar la distancia entre ciudades:

.. list-table::
   :header-rows: 1
   :widths: 12 14 14 14

   * -
     - Tijuana
     - Ensenada
     - Mexicali
   * - Tijuana
     - 0
     - 104.2
     - 185.5
   * - Ensenada
     - 104.2
     - 0
     - 240.3
   * - Mexicali
     - 185.5
     - 240.3
     - 0

O una matriz binaria que indica qué cursos ha tomado cada persona:

.. list-table::
   :header-rows: 1
   :widths: 10 22 12 22

   * -
     - Cloud Computing
     - Big Data
     - Complex Systems
   * - Ana
     - 1
     - 1
     - 1
   * - Tom
     - 0
     - 1
     - 0
   * - Joe
     - 0
     - 0
     - 0

Algunas matrices tienen propiedades interesantes: por ejemplo, la matriz de distancias es
simétrica, mientras que otras pueden ser binarias o dispersas (*sparse*).

A continuación veremos ejemplos de matrices comunes en minería de datos.

Documento–término
^^^^^^^^^^^^^^^^^

Esta matriz describe una colección de documentos a través de los términos que aparecen en ellos.
Cada renglón representa un documento y cada columna un término. En cada celda se coloca un valor
representativo de la relación documento–término, normalmente relacionado con frecuencias.

Una representación común es el peso **TF–IDF** (*term frequency – inverse document frequency*),
que veremos a detalle en la sección de preprocesamiento.

Para entender la idea, consideremos estos dos documentos:

.. code-block:: text

   D1: "La vida no vale nada, no vale nada la vida"
   D2: "Toda la vida coleccionado mil amores"

Podemos representarlos con una matriz de frecuencias:

.. list-table::
   :header-rows: 1
   :widths: 10 8 6 8 8 16 6 10

   * -
     - vida
     - no
     - vale
     - toda
     - coleccionando
     - mil
     - amores
   * - D1
     - 2
     - 2
     - 2
     - 0
     - 0
     - 0
     - 0
   * - D2
     - 1
     - 0
     - 0
     - 1
     - 1
     - 1
     - 1

En la práctica, también se suelen remover términos demasiado comunes (palabras vacías), ya que
aparecen en casi todos los documentos y no distinguen contenido.

Una característica importante de la matriz documento–término es que suele ser **escasa**
(muchos ceros). Por eso, se implementa con estructuras especiales para ahorrar memoria y
acelerar cálculos.

Filtrado colaborativo
^^^^^^^^^^^^^^^^^^^^^

Los sistemas de recomendación buscan sugerir artículos (películas, restaurantes, libros) de
manera personalizada. Una técnica clásica es el **filtrado colaborativo**, que parte de una
matriz de calificaciones usuario–artículo.

Por ejemplo, en un sistema de películas:

.. list-table::
   :header-rows: 1
   :widths: 10 18 20 22

   * -
     - The Matrix
     - Matando Cabos
     - Mad Max: Fury Road
   * - Ana
     - 0
     - 1
     - 5
   * - Tom
     - 4
     - 4
     - 0
   * - Joe
     - 5
     - 5
     - 5
   * - Tim
     - 0
     - 1
     - 5

De nuevo, esta matriz tiende a ser escasa. Comúnmente el 0 indica que el usuario aún no ha
calificado el artículo. Las calificaciones suelen ser discretas y positivas.

Esta matriz permite recomendar de dos maneras:

- buscando usuarios similares,
- o buscando artículos similares.

En la sección de sistemas de recomendación veremos este tema con más detalle.

Grafos
~~~~~~

Al igual que las matrices, los grafos representan relaciones entre objetos; sin embargo, lo
hacen de una manera más natural cuando hay múltiples tipos de relaciones u objetos.

Un grafo que relacione objetos del mismo tipo puede representarse como matriz de adyacencia.
Por ejemplo, en Twitter existe la relación *sigue a* entre usuarios. Esta matriz no es simétrica:
Tom puede seguir a Ana sin que Ana siga a Tom.

.. list-table::
   :header-rows: 1
   :widths: 10 8 8 8 8

   * -
     - Ana
     - Tom
     - Joe
     - Tim
   * - Ana
     - 0
     - 0
     - 0
     - 0
   * - Tom
     - 1
     - 0
     - 1
     - 0
   * - Joe
     - 1
     - 1
     - 0
     - 0
   * - Tim
     - 1
     - 1
     - 1
     - 0

Estas relaciones también se pueden representar como un grafo:

.. figure:: _static/img/grafo1.png
   :width: 70%
   :align: center
   :alt: Una matriz expresada como grafo

   Una matriz expresada en forma de grafo.

Un grafo también puede representar múltiples tipos de relaciones y objetos al mismo tiempo:

.. figure:: _static/img/grafo2.png
   :width: 70%
   :align: center
   :alt: Grafo con distintos tipos de relaciones

   Ejemplo de un grafo con distintos tipos de relaciones.

Mientras algunos algoritmos pueden trabajar directamente con grafos, otros requieren su
representación matricial.

A lo largo del material utilizaremos estos tres tipos de conjuntos de datos. Existen variantes
y otros tipos especializados: imágenes, video, gestos, genoma humano, series de tiempo, y muchos
más. Recuerda que este material es solo el inicio: conforme avances, encontrarás cursos, tutoriales,
videos y artículos para profundizar.

Ejercicios
----------

Da un ejemplo de cada concepto:

- Observación
- Dato
- Objeto
- Atributo
- Medida
- Categórico
- Nominal
- Razón
- Ordinal
- Intervalar
- Conjunto de datos
- Tabla
- Matriz
- Grafo

Lectura adicional
-----------------

- Sobre la Teoría de las Escalas de Medición:
  http://berniecl.weebly.com/uploads/7/2/5/3/72531/02_s.s._stevens_-_sobre_teora_escalas_de_medicin_esp.pdf

