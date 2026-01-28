Ejercicio: ¿Setosa, Virginica o Versicolor?
===========================================

En esta sección haremos un ejercicio práctico del proceso de KDD. Utilizaremos Python
y algunas bibliotecas que ocuparemos a lo largo del libro. Al terminar, tendrás una
idea del tipo de programación que se requiere para el análisis de datos en Python.

.. note::

   Para realizar los ejercicios se recomienda tener instalada la distribución
   `Anaconda <https://www.anaconda.com/download>`_. En el texto original se menciona
   Python 2.7; si tu curso ya trabaja con Python 3, haremos los ajustes en el código
   cuando formalicemos el entorno del libro.

Como primer ejercicio seguiremos el proceso de KDD para extraer conocimiento a partir del
famoso **conjunto de datos de flores Iris**. Este conjunto de datos incluso cuenta con su
propia entrada en Wikipedia.

El conjunto fue introducido por Ronald Fisher en 1936. Contiene 50 muestras de tres
especies de la flor Iris (*Iris setosa*, *Iris virginica* e *Iris versicolor*).
Fisher midió cuatro características de cada muestra: largo y ancho del sépalo, y largo
y ancho del pétalo (en centímetros). Con base en estos rasgos, Fisher desarrolló un
modelo discriminante lineal para distinguir entre especies.

Como ejemplo, veamos un fragmento con varios registros:

iris.data
---------

.. code-block:: text

   sepal_length,sepal_width,petal_length,petal_width,species
   5.1,3.5,1.4,0.2,setosa
   4.9,3.0,1.4,0.2,setosa
   4.7,3.2,1.3,0.2,setosa
   4.6,3.1,1.5,0.2,setosa
   7.0,3.2,4.7,1.4,versicolor
   6.4,3.2,4.5,1.5,versicolor
   6.9,3.1,4.9,1.5,versicolor
   5.5,2.3,4.0,1.3,versicolor
   6.5,2.8,4.6,1.5,versicolor
   6.3,3.3,6.0,2.5,virginica
   5.8,2.7,5.1,1.9,virginica
   7.1,3.0,5.9,2.1,virginica
   6.3,2.9,5.6,1.8,virginica
   6.5,3.0,5.8,2.2,virginica

Para compartir conjuntos de datos normalmente se utilizan archivos en formato texto.
En este caso, el *dataset* está en formato CSV (*comma-separated values*), lo cual
facilita representar datos tabulares. Los archivos CSV se pueden abrir e importar
sin problema a hojas de cálculo y sistemas de bases de datos.

Como vemos en el ejemplo, los registros están separados por saltos de línea y la
primera fila indica el nombre de cada columna. En particular, cada registro tiene
cinco campos: los primeros cuatro son medidas en centímetros y el último es la
**etiqueta** (la clase), que indica el tipo de flor.

Paso 1: Identificar el objetivo
-------------------------------

El objetivo del proceso de KDD será encontrar patrones (modelos) que nos permitan
clasificar las flores o, en general, extraer conocimiento útil a partir de los datos.

Paso 2: Seleccionar y recolectar
--------------------------------

En este caso necesitamos obtener el archivo *iris.data* (o una variante en CSV).
Podemos hacerlo manualmente o directamente con Python.

Recolectar el archivo ``iris.data``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Lo haremos de manera interactiva desde el intérprete. Para ejecutarlo, escribe
``python`` en la línea de comandos.

El intérprete muestra un mensaje de bienvenida. Una sesión típica se vería como:

.. code-block:: text

   Python 2.7.13 |Anaconda custom (x86_64)| ...
   >>>

El archivo puede descargarse del *Machine Learning Repository* de la UCI.
La URL directa es:

- https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data

En ocasiones el servidor no está disponible; si eso sucede, descárgalo de otra fuente.

Para bajarlo utilizaremos la biblioteca de Python ``requests`` (incluida en Anaconda):

.. code-block:: python

   >>> import requests

Hacemos la petición *GET* al archivo (puede tardar unos segundos):

.. code-block:: python

   >>> r = requests.get("https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data")

Imprimimos el texto para comprobar que se descargó bien (fragmento):

.. code-block:: python

   >>> r.text
   u"5.7,3.8,1.7,0.3,Iris-setosa\n5.1,3.8,1.5,0.3,Iris-setosa\n..."

¡Muy bien! Continuemos.

Paso 3: Preprocesar los datos
-----------------------------

Por ahora tenemos una cadena enorme con todo el contenido del archivo, lo cual no es
muy útil para análisis numérico. El objetivo de este paso será transformar esa cadena
en una estructura numérica, en particular, un arreglo multidimensional de NumPy.

.. note::

   En este capítulo hacemos el preprocesamiento “a mano” para entender qué está pasando.
   Más adelante veremos bibliotecas que simplifican este proceso, como `pandas
   <https://pandas.pydata.org/>`_.

Empezaremos por separar el texto en renglones usando ``split()`` con saltos de línea:

.. code-block:: python

   >>> renglones_iris = r.text.split("\n")

Revisemos el contenido:

.. code-block:: python

   >>> renglones_iris[:5]
   [u"5.1,3.5,1.4,0.2,Iris-setosa", u"4.9,3.0,1.4,0.2,Iris-setosa", ...]

Observa un detalle importante: al final suelen aparecer renglones vacíos:

.. code-block:: python

   >>> renglones_iris[-4:]
   [u"6.2,3.4,5.4,2.3,Iris-virginica", u"5.9,3.0,5.1,1.8,Iris-virginica", u"", u""]

Cada renglón tiene 5 campos separados por comas, pero para el análisis inicial
queremos **solo las 4 medidas** (no la etiqueta). Vamos a crear una lista nueva
usando una comprensión de listas.

Primero, separamos por comas y nos quedamos con los primeros 4 elementos:

.. code-block:: python

   >>> [renglon.split(",")[:-1] for renglon in renglones_iris[:-2]]
   [[u"5.1", u"3.5", u"1.4", u"0.2"], [u"4.9", u"3.0", u"1.4", u"0.2"], ...]

Notemos que todavía son cadenas; necesitamos convertirlas a flotantes. Para ello
utilizamos ``map`` con ``float``:

.. code-block:: python

   >>> [map(float, renglon.split(",")[:-1]) for renglon in renglones_iris[:-2]]
   [[5.1, 3.5, 1.4, 0.2], [4.9, 3.0, 1.4, 0.2], ...]

Ya que validamos el procedimiento paso a paso, creamos el arreglo:

.. code-block:: python

   >>> import numpy as np
   >>> iris_data = [map(float, renglon.split(",")[:-1]) for renglon in renglones_iris[:-2]]
   >>> iris = np.array(iris_data)

Listo: ya tenemos los datos en una estructura adecuada:

.. code-block:: python

   >>> iris[:10]
   array([[5.1, 3.5, 1.4, 0.2],
          [4.9, 3. , 1.4, 0.2],
          [4.7, 3.2, 1.3, 0.2],
          [4.6, 3.1, 1.5, 0.2],
          [5. , 3.6, 1.4, 0.2],
          [5.4, 3.9, 1.7, 0.4],
          [4.6, 3.4, 1.4, 0.3],
          [5. , 3.4, 1.5, 0.2],
          [4.4, 2.9, 1.4, 0.2],
          [4.9, 3.1, 1.5, 0.1]])

Paso 4: Transformar los datos
-----------------------------

En este caso sencillo no será necesario transformar los datos.

Paso 5: Seleccionar la tarea de minería de datos
------------------------------------------------

La tarea que realizaremos será **clasificación**.

Paso 6: Análisis exploratorio
-----------------------------

En este primer ejercicio haremos minería de datos de forma manual, explorando los datos
visualmente. Para ello utilizaremos `matplotlib <https://matplotlib.org/>`_.

Primero seleccionamos dos características: **largo** y **ancho** del sépalo, para ver si
hay algún patrón útil. Los primeros 50 registros corresponden a *Iris setosa*.

.. code-block:: python

   >>> import matplotlib.pyplot as plt
   >>> x = iris[:50, 0]   # sepal_length
   >>> y = iris[:50, 1]   # sepal_width
   >>> plt.plot(x, y, "r.")
   >>> plt.show()

Al ejecutar ``plt.show()`` se debería mostrar una nube de puntos similar a la de la
Figura siguiente:

.. figure:: _static/img/plot1.png
   :width: 70%
   :align: center
   :alt: Iris setosa usando sepal_length y sepal_width

   Iris setosa en el plano (sepal_length, sepal_width).

Ahora graficaremos *setosa* y *versicolor* al mismo tiempo:

.. code-block:: python

   >>> plt.plot(iris[:50, 0], iris[:50, 1], "r.")       # Setosa
   >>> plt.plot(iris[50:100, 0], iris[50:100, 1], "b.") # Versicolor
   >>> plt.show()

.. figure:: _static/img/plot2.png
   :width: 70%
   :align: center
   :alt: Setosa y Versicolor

   Setosa y Versicolor (dos características).

Finalmente agregamos *virginica*:

.. code-block:: python

   >>> plt.plot(iris[:50, 0], iris[:50, 1], "r.")         # Setosa
   >>> plt.plot(iris[50:100, 0], iris[50:100, 1], "b.")   # Versicolor
   >>> plt.plot(iris[100:, 0], iris[100:, 1], "g.")       # Virginica
   >>> plt.show()

.. figure:: _static/img/plot3.png
   :width: 70%
   :align: center
   :alt: Setosa, Versicolor y Virginica

   Setosa, Versicolor y Virginica.

A simple vista, con estas dos características parece fácil separar *setosa* del resto,
pero distinguir entre *versicolor* y *virginica* es más complicado.

Paso 7: Minería de datos
------------------------

Por ahora haremos la minería de datos manualmente: queremos proponer un **modelo**
que separe las clases. Una idea muy simple es usar una recta para separar a *setosa*
del resto.

.. figure:: _static/img/plot4.png
   :width: 70%
   :align: center
   :alt: Separación lineal de Setosa

   Separación lineal (manual) de Setosa contra el resto.

Otra forma de expresar un modelo es mediante reglas:

.. code-block:: text

   R1:
       SI sepal_length < 5.9 AND sepal_width > 2.9
       ENTONCES:
            Setosa

   R2:
       SI sepal_length < 4.7 AND sepal_width <= 2.9
       ENTONCES:
            Setosa

Gráficamente sería algo como lo siguiente:

.. figure:: _static/img/plot5.png
   :width: 70%
   :align: center
   :alt: Reglas para Setosa

   Reglas (manuales) para identificar Setosa.

Podemos proponer un modelo refinado agregando otra regla:

.. figure:: _static/img/plot6.png
   :width: 70%
   :align: center
   :alt: Modelo refinado con reglas

   Modelo refinado (manual) con reglas adicionales.

Podríamos seguir buscando variando los umbrales (por ejemplo, cambiar
``sepal_length < 5.9`` por ``sepal_length < 6.0``), pero rápidamente se vuelve claro
que **hacer esto a mano no es práctico**.

La solución es diseñar programas que generen modelos automáticamente: es decir,
aplicar **algoritmos de aprendizaje automático**.

Paso 8: Interpretar y evaluar
-----------------------------

Esto lo desarrollaremos más adelante, pero vale la pena plantear preguntas desde ahora:

- ¿Cuál es el mejor modelo?
- ¿El mejor modelo es suficientemente bueno?
- ¿Existe un modelo óptimo?
- ¿Qué pasará cuando agreguemos nuevas flores al conjunto de datos?

También conviene preguntarnos si extraímos conocimiento útil: ¿qué aprendimos realmente?

Paso 9: Aplicar el conocimiento adquirido
-----------------------------------------

De este ejercicio podríamos reportar, por ejemplo, que *Iris setosa* es relativamente
fácil de identificar con dos características del sépalo.

Este ejercicio es básico y no hicimos todas las consideraciones. Más adelante
incorporaremos evaluación formal, validación y modelos automáticos (ya no minería
manual). Además, el ejercicio sirve para darnos una idea de cómo trabajaremos con
Python y sus bibliotecas: de forma interactiva, componiendo funciones, y usando
estructuras numéricas como arreglos.

.. warning::

   Para estudiantes:
   El proceso de KDD también puede usarse como guía para proyectos de investigación.
   El proceso general es similar, aunque cambian los algoritmos, aplicaciones y tipos
   de datos.

