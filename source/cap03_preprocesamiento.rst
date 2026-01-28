Preprocesamiento
================

En esta etapa vamos a procesar los objetos crudos para pasarlos a representaciones favorables para
los algoritmos que utilizaremos más adelante. También es conveniente reducir, en lo posible, la
cantidad de datos y el número de atributos para no incrementar el costo computacional.

Para lograr lo anterior, en la etapa de preprocesamiento podemos realizar tareas como:

- Estandarización
- Escalado
- Normalización
- Agregación
- Codificación de atributos categóricos
- Muestreo
- Reducción de la dimensión
- Creación de características
- Transformación de atributos

Estandarización
---------------

Muchos algoritmos de aprendizaje automático requieren que los datos estén estandarizados o, al
menos, en escalas comparables para funcionar correctamente. Vamos a entender la razón mediante un
ejemplo. Observemos de nuevo los datos ``auto-mpg``:

.. code-block:: python

   >>> import numpy as np
   >>> import pandas as pd
   >>> import matplotlib.pyplot as plt
   >>> auto_mpg = pd.read_csv(
   ...     "datos-ejemplo/auto-mpg.data",
   ...     sep=r"\s+",
   ...     header=None,
   ...     na_values="?",
   ...     names=[
   ...         "mpg", "cylinders", "displacement", "horsepower",
   ...         "weight", "acceleration", "model_year", "origin", "car_name"
   ...     ],
   ...     dtype={
   ...         "mpg": "f4",
   ...         "cylinders": "i4",
   ...         "displacement": "f4",
   ...         "horsepower": "f4",
   ...         "weight": "f4",
   ...         "acceleration": "f4",
   ...         "model_year": "i4",
   ...         "origin": "category",
   ...         "car_name": "category",
   ...     }
   ... )
   >>> auto_mpg["origin"].cat.categories = ["USA", "Japan", "Germany"]
   >>> auto_mpg.tail(3)
         mpg  cylinders  displacement  horsepower  weight  acceleration  \
   395  32.0          4         135.0        84.0  2295.0          11.6
   396  28.0          4         120.0        79.0  2625.0          18.6
   397  31.0          4         119.0        82.0  2720.0          19.4

        model_year origin       car_name
   395          82    USA  dodge rampage
   396          82    USA    ford ranger
   397          82    USA     chevy s-10

Nos concentraremos en dos atributos: ``weight`` y ``mpg``. Vamos a graficar los objetos:

.. code-block:: python

   >>> auto_mpg.plot.scatter(x="weight", y="mpg")
   >>> plt.show()

.. figure:: _static/img/mpg-pre.png
   :width: 70%
   :align: center
   :alt: Diagrama de dispersión weight vs mpg

   Relación entre ``weight`` y ``mpg`` en ``auto-mpg``.

Al observar la gráfica (o la tabla) notamos que las escalas numéricas son muy distintas: una
diferencia de 20 unidades en ``mpg`` es grande, mientras que en ``weight`` no lo es.

Ahora veamos la distribución con histogramas:

.. code-block:: python

   >>> auto_mpg.hist(column="weight")
   >>> plt.show()

.. figure:: _static/img/mpg-weight-hist.png
   :width: 70%
   :align: center
   :alt: Histograma de weight

   Histograma de ``weight``.

.. code-block:: python

   >>> auto_mpg.hist(column="mpg")
   >>> plt.show()

.. figure:: _static/img/mpg-mpg-hist.png
   :width: 70%
   :align: center
   :alt: Histograma de mpg

   Histograma de ``mpg``.

Algunos valores se repiten más que otros. Por ejemplo, un auto con 40 mpg es mucho menos probable
que uno con 20 mpg. Si asumiéramos una distribución normal, podríamos estimar probabilidades a
partir de la media y la desviación estándar.

Esto se simplifica si trabajamos con una normal estandarizada, es decir, una distribución con
media 0 y desviación estándar 1. Para transformar un valor :math:`X` usamos el *z-score*:

.. math::

   Z = \frac{X - \mu}{\sigma}

donde :math:`\mu` es la media y :math:`\sigma` la desviación estándar.

Podemos visualizar la normal estandarizada:

.. code-block:: python

   >>> import math
   >>> import matplotlib.mlab as mlab
   >>> mu = 0
   >>> variance = 1
   >>> sigma = math.sqrt(variance)
   >>> x = np.linspace(mu - 3*sigma, mu + 3*sigma, 100)
   >>> plt.plot(x, mlab.normpdf(x, mu, sigma))
   >>> plt.show()

.. figure:: _static/img/estandar.png
   :width: 70%
   :align: center
   :alt: Distribución normal estandarizada

   Distribución normal estandarizada.

Podemos estandarizar los datos con ``scikit-learn``:

.. code-block:: python

   >>> from sklearn import preprocessing
   >>> datos = auto_mpg.loc[:, ["mpg", "weight"]]
   >>> datos_estandarizados = preprocessing.scale(datos)
   >>> df = pd.DataFrame(datos_estandarizados, columns=["mpg", "weight"])
   >>> df.plot.scatter(x="weight", y="mpg")
   >>> plt.show()

.. figure:: _static/img/mpg-weight-plot.png
   :width: 70%
   :align: center
   :alt: Dispersión tras estandarización

   ``weight`` vs ``mpg`` después de estandarizar.

Ahora los valores numéricos están en escalas comparables. Si graficamos el histograma de ``mpg``:

.. code-block:: python

   >>> df.hist(column="mpg")
   >>> plt.show()

.. figure:: _static/img/mpg-hist-estandar.png
   :width: 70%
   :align: center
   :alt: Histograma de mpg estandarizado

   Histograma de ``mpg`` tras estandarización.

Observa que la distribución sigue sesgada; estandarizar no “hace normal” una variable: solo la
reescala con respecto a su media y desviación.

En muchos casos repetiremos este proceso, por lo que ``preprocessing`` incluye ``StandardScaler``
para ajustar (``fit``) media y desviación en entrenamiento y aplicar (``transform``) la misma
transformación en prueba. Más adelante hablaremos con detalle sobre conjuntos de entrenamiento y
prueba.

Escalado a un rango
-------------------

Otra transformación posible para ``weight`` y ``mpg`` es llevarlos a un rango entre 0 y 1:

.. code-block:: python

   >>> datos = auto_mpg.loc[:, ["mpg", "weight"]]
   >>> min_max_scaler = preprocessing.MinMaxScaler()
   >>> datos_minmax = min_max_scaler.fit_transform(datos)
   >>> df = pd.DataFrame(datos_minmax, columns=["mpg", "weight"])
   >>> df.plot.scatter(x="weight", y="mpg")
   >>> plt.show()

.. figure:: _static/img/mpg-weight-range.png
   :width: 70%
   :align: center
   :alt: Escalado MinMax

   Escalado a rango con ``MinMaxScaler``.

Para escalar a otros rangos enviamos una tupla al constructor:

.. code-block:: python

   >>> min_val = -1
   >>> max_val = 1
   >>> min_max_scaler = preprocessing.MinMaxScaler(feature_range=(min_val, max_val))

Escalado con valores atípicos
-----------------------------

Si los datos incluyen valores atípicos, conviene usar estadísticas robustas. Una estrategia es
usar la mediana y escalar con base en percentiles. La clase ``RobustScaler`` de scikit-learn,
por defecto, utiliza el rango intercuartílico (entre percentil 25% y 75%).

.. code-block:: python

   >>> datos = auto_mpg.loc[:, ["mpg", "weight"]]
   >>> robust_scaler = preprocessing.RobustScaler()
   >>> datos_robustos = robust_scaler.fit_transform(datos)
   >>> df = pd.DataFrame(datos_robustos, columns=["mpg", "weight"])
   >>> df.plot.scatter(x="weight", y="mpg")
   >>> plt.show()

.. figure:: _static/img/mpg-robust.png
   :width: 70%
   :align: center
   :alt: Escalado robusto

   Escalado robusto con ``RobustScaler``.

Medidas de similaridad y normalización
--------------------------------------

Algunos algoritmos de clasificación de textos utilizan una representación de documentos llamada
modelo de espacio vectorial. En este modelo, cada documento se representa como un vector donde
cada componente indica la ponderación de cierto término.

Por ejemplo, consideremos cinco documentos representados por términos deportivos:

.. list-table::
   :header-rows: 1
   :widths: 8 12 8 10 10 10 12

   * -
     - Home Run
     - Gol
     - Jugador
     - Penal
     - Tiro
     - Doble Play
   * - d1
     - 3
     - 0
     - 3
     - 0
     - 4
     - 2
   * - d2
     - 0
     - 5
     - 7
     - 3
     - 2
     - 0
   * - d3
     - 0
     - 0
     - 3
     - 3
     - 1
     - 0
   * - d4
     - 5
     - 0
     - 6
     - 0
     - 2
     - 3
   * - d5
     - 5
     - 0
     - 3
     - 0
     - 3
     - 3

Una forma simple de medir qué tan parecidos son dos documentos es el **producto punto** entre
sus vectores; en términos geométricos, la similaridad se relaciona con el ángulo entre ellos.

Consideremos un caso básico con dos términos: *Jugador* y *Tiro*. Entonces cada documento es un
vector en :math:`\mathbb{R}^2`:

.. figure:: _static/img/vectors-docs.png
   :width: 70%
   :align: center
   :alt: Vectores documento en 2D

   Representación de documentos como vectores.

El cálculo de ángulos se simplifica si todos los vectores tienen longitud unitaria. Por eso es
común normalizar dividiendo por la norma euclidiana (norma :math:`L_2`).

.. figure:: _static/img/norm.png
   :width: 70%
   :align: center
   :alt: Normalización L2

   Normalización con norma :math:`L_2`.

.. code-block:: python

   >>> from sklearn import preprocessing
   >>> import numpy as np
   >>> X = np.array([[3., 4.], [7., 2.], [3., 1.], [6., 2.], [3., 3.]])
   >>> X_normalized = preprocessing.normalize(X, norm="l2")
   >>> X_normalized
   array([[0.6       , 0.8       ],
          [0.96152395, 0.27472113],
          [0.9486833 , 0.31622777],
          [0.9486833 , 0.31622777],
          [0.70710678, 0.70710678]])

En este ejemplo, ``d3`` y ``d4`` tienen la misma dirección; el producto punto da 1 (salvo error
numérico):

.. code-block:: python

   >>> np.dot(X_normalized[2], X_normalized[3])
   0.9999999999999999

En ocasiones también se utiliza la normalización con norma :math:`L_1` (asociada a la distancia
Manhattan):

.. figure:: _static/img/norma.png
   :width: 70%
   :align: center
   :alt: Normalización L1

   Normalización con norma :math:`L_1`.

.. code-block:: python

   >>> X_manhattan = preprocessing.normalize(X, norm="l1")
   >>> X_manhattan
   array([[0.42857143, 0.57142857],
          [0.77777778, 0.22222222],
          [0.75      , 0.25      ],
          [0.75      , 0.25      ],
          [0.5       , 0.5       ]])

Datos agregados
---------------

Imagina que eres el ejecutivo de una cadena de librerías y tienes que decidir qué productos se
promoverán en la siguiente temporada. Para apoyarte, usas un sistema OLAP que permite ver ventas
con distintos niveles de detalle.

Nivel de granularidad bajo (ventas diarias por tienda):

.. list-table::
   :header-rows: 1
   :widths: 18 10 10 10 10 14 8 8

   * - Producto
     - Cantidad
     - Tienda
     - Ciudad
     - País
     - Fecha
     - Mes
     - Año
   * - El Principito
     - 3
     - 123
     - LA
     - USA
     - 10/02/16
     - FEB
     - 2016
   * - El Principito
     - 0
     - 156
     - LA
     - USA
     - 10/02/16
     - FEB
     - 2016
   * - El Hobbit
     - 5
     - 123
     - LA
     - USA
     - 10/02/16
     - FEB
     - 2016
   * - El Hobbit
     - 1
     - 156
     - LA
     - USA
     - 10/02/16
     - FEB
     - 2016

Una vista más agregada (por ciudad y mes):

.. list-table::
   :header-rows: 1
   :widths: 18 10 10 10 8 8

   * - Producto
     - Cantidad
     - Ciudad
     - País
     - Mes
     - Año
   * - El Principito
     - 10
     - LA
     - USA
     - ENE
     - 2016
   * - El Principito
     - 30
     - LA
     - USA
     - FEB
     - 2016
   * - El Hobbit
     - 30
     - LA
     - USA
     - ENE
     - 2016
   * - El Hobbit
     - 30
     - LA
     - USA
     - FEB
     - 2016

Incluso más agregada (por país y año):

.. list-table::
   :header-rows: 1
   :widths: 18 12 12 8

   * - Producto
     - Cantidad
     - País
     - Año
   * - El Principito
     - 29203
     - México
     - 2016
   * - El Principito
     - 29788
     - USA
     - 2016
   * - El Hobbit
     - 18987
     - México
     - 2017
   * - El Hobbit
     - 36699
     - USA
     - 2017

Agregar datos consiste en agrupar por uno o más atributos y aplicar una operación (por ejemplo,
sumar). Al reducir el número de registros, la agregación tiene ventajas:

- Si solo almacenamos datos agregados, ahorramos espacio.
- Se reduce el nivel de detalle (granularidad) y, a veces, el número efectivo de variables.
- La variabilidad suele disminuir.

Los datos agregados son fundamentales en sistemas de toma de decisiones, almacenes de datos y
cubos de datos. Este tema se revisa con más detalle en la sección de OLAP.

Codificación de atributos categóricos
-------------------------------------

Muchos algoritmos de aprendizaje automático solo reciben como entrada vectores con valores
numéricos. ¿Qué hacemos con atributos categóricos?

Una solución ingenua es asignar enteros a las categorías (por ejemplo, ``masculino -> 0`` y
``femenino -> 1``). El problema es que eso introduce un orden artificial, lo cual puede ser
incorrecto cuando la variable es nominal.

Una alternativa estándar es la **codificación one-hot** (representación binaria): se crea un
atributo por cada categoría posible.

Ejemplo original:

.. list-table::
   :header-rows: 1
   :widths: 14 18

   * - Nombre
     - Género
   * - Ana
     - Femenino
   * - Tom
     - Masculino
   * - Sue
     - Femenino
   * - Jim
     - Masculino

Representación binaria:

.. list-table::
   :header-rows: 1
   :widths: 14 14 14

   * - Nombre
     - Femenino
     - Masculino
   * - Ana
     - 1
     - 0
   * - Tom
     - 0
     - 1
   * - Sue
     - 1
     - 0
   * - Jim
     - 0
     - 1

Esta representación transforma una variable nominal con :math:`n` categorías en :math:`n` variables
binarias, activando con 1 la categoría correspondiente.

Practiquemos con el atributo ``origin`` del conjunto ``auto_mpg``. Recordemos que ``origin`` toma
valores categóricos que corresponden a ``["USA", "Japan", "Germany"]``. Usaremos ``OneHotEncoder``
de scikit-learn.

.. code-block:: python

   >>> import numpy as np
   >>> import pandas as pd
   >>> import matplotlib.pyplot as plt
   >>> from sklearn import preprocessing
   >>>
   >>> auto_mpg = pd.read_csv(
   ...     "auto-mpg.data",
   ...     sep=r"\s+",
   ...     header=None,
   ...     na_values="?",
   ...     names=[
   ...         "mpg", "cylinders", "displacement", "horsepower",
   ...         "weight", "acceleration", "model_year", "origin", "car_name"
   ...     ],
   ...     dtype={
   ...         "mpg": "f4",
   ...         "cylinders": "i4",
   ...         "displacement": "f4",
   ...         "horsepower": "f4",
   ...         "weight": "f4",
   ...         "acceleration": "f4",
   ...         "model_year": "i4",
   ...         "origin": "category",
   ...         "car_name": "category",
   ...     }
   ... )
   >>> auto_mpg["origin"].cat.categories = ["USA", "Japan", "Germany"]
   >>>
   >>> enc = preprocessing.OneHotEncoder()
   >>> enc.fit(auto_mpg[["origin"]])
   >>> a = enc.transform(auto_mpg[["origin"]])
   >>> a.toarray()[0]
   array([1., 0., 0.])

Muestreo
--------

En estadística, el muestreo es una herramienta de investigación científica cuyo objetivo es
seleccionar una muestra de objetos de una población para hacer inferencias sobre ella.

Trabajamos con muestras porque puede ser difícil o costoso analizar a toda la población. Si la
muestra es representativa, reducimos costos llegando a conclusiones similares.

En minería de datos podría parecer que tenemos el problema contrario: partimos de datos masivos
con muchos atributos. Sin embargo, precisamente por esa escala, **usamos muestreo** para reducir
costo computacional y para evaluar modelos y comparar algoritmos.

Las técnicas de muestreo en minería de datos se basan en métodos estándar de estadística: muestreo
aleatorio simple o estratificado, con o sin reemplazo, entre otros. Para evaluar desempeño se usan
técnicas de partición como *leave-one-out* y validación cruzada de :math:`k` particiones
(*k-fold cross validation*).

Muestreo aleatorio simple
~~~~~~~~~~~~~~~~~~~~~~~~~

Puede aplicarse cuando podemos calcular la probabilidad de selección de cada objeto. En minería de
datos, a menudo tenemos control sobre la selección en datos electrónicos, pero debemos cuidar
sesgos de origen: la muestra puede ser “representativa” del archivo disponible y no del fenómeno
real.

Dos variantes:

- **Sin reemplazo.** Cada objeto seleccionado se extrae y ya no puede volver a seleccionarse. La
  probabilidad cambia conforme disminuye el tamaño de la población.

- **Con reemplazo.** Los objetos se reinsertan; un mismo objeto puede seleccionarse más de una vez.
  El tamaño de la población permanece constante.

Muestreo sistemático
~~~~~~~~~~~~~~~~~~~~

Se selecciona un punto de inicio al azar y, con un intervalo fijo, se eligen objetos “saltando” en
una lista circular ordenada.

Muestreo estratificado
~~~~~~~~~~~~~~~~~~~~~~

Cuando existen subgrupos importantes pero poco frecuentes, se asigna una cuota por estrato (por
ejemplo, 100 futbolistas y 2 jugadores de polo) y dentro de cada estrato se muestrea aleatoriamente
(o de forma sistemática).

Muestreo por conglomerados (*cluster sampling*)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Cuando hay conglomerados relativamente homogéneos (por ejemplo, estudiantes por campus), se eligen
al azar algunos conglomerados y dentro de ellos se toma una muestra. Esto reduce costos logísticos.

Muestreo aleatorio simple en Python
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

En algunos casos basta con la biblioteca estándar ``random`` para muestrear secuencias:

.. code-block:: python

   >>> import random
   >>> pop = ["juan", "luis", "paco", "pedro", "jorge", "victor", "ana", "ada", "megan", "emma"]
   >>> random.sample(pop, 3)
   ["juan", "luis", "emma"]
   >>> random.sample(range(100000000), 3)
   [67307206, 72755273, 50751539]

