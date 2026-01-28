Clasificación Básica
====================

Preliminares
------------

Clasificar consiste en asignar a los objetos una **categoría**, seleccionada de un conjunto de
categorías previamente definido. Esta es una tarea necesaria en muchos sistemas informáticos.
Por ejemplo, una funcionalidad básica de los servicios de correo electrónico es asignar
automáticamente los mensajes recibidos a una de dos categorías: *correo válido* o *correo no deseado*.

Para clasificar correos, el sistema considera ciertas características del mensaje, como el título y el
texto del contenido. Podemos ver a un clasificador como una función que toma las características de un
objeto y regresa la categoría a la que pertenece.

Un ejemplo de un clasificador muy sencillo podría ser uno que busque términos típicos de *spam*:

.. code-block:: python

   def clasificador(mensaje):
       for palabra in ["Rolex", "Buy Bitcoins", "Free Vacations"]:
           if palabra in mensaje:
               return "spam"
       return "ham"

Este clasificador no es muy bueno: los creadores de correos no deseados podrían hacer variaciones
para engañarlo (por ejemplo, cambiar ``Free Vacations`` por ``Free-Vacations``).

Una mejor idea es ver la clasificación como un **generador de funciones de clasificación**.
Tomando como entrada un conjunto de correos previamente clasificados, el generador debe ser capaz de
construir automáticamente una función que clasifique correos correctamente.

En esta sección conoceremos los conceptos básicos de clasificación, veremos cómo evaluar el desempeño
de clasificadores y, finalmente, cómo comparar distintas técnicas de clasificación.

Un clasificador (o técnica de clasificación) es un algoritmo cuyo objetivo es generar funciones o
modelos de clasificación a partir de un conjunto de datos de entrenamiento. El conjunto de entrenamiento
consiste en registros compuestos por:

- un vector de características, y
- la categoría (etiqueta o clase) a la que pertenece el objeto.

Podemos representar cada registro como una tupla :math:`(\vec{x}, y)`, donde :math:`\vec{x}` es el
vector de características y :math:`y` la categoría.

Recordemos el conjunto de datos Iris:

.. list-table::
   :header-rows: 1
   :widths: 12 12 12 12 12

   * - sepal_length
     - sepal_width
     - petal_length
     - petal_width
     - label
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

En este caso cada registro tiene un vector con cuatro características continuas y la clase de la flor
(es decir, ``setosa``, ``virginica`` o ``versicolor``).

El modelo que genera el algoritmo puede aplicarse a nuevos datos. Para estimar qué tan bueno es el
clasificador, lo aplicamos a un conjunto de datos **de prueba**, cuyas etiquetas se consideran
“ocultas” durante la predicción. Luego comparamos las predicciones con las etiquetas reales.

En la figura siguiente se muestra el proceso general del aprendizaje supervisado:

.. figure:: _static/img/clasificador.png
   :width: 70%
   :align: center
   :alt: Esquema de aprendizaje supervisado

   Aprendizaje supervisado.

Al final obtenemos dos vectores: :math:`y` con las categorías reales y :math:`\hat{y}` con las
categorías predichas por el modelo. Si ambos vectores fueran idénticos, el modelo no tendría errores
(en ese conjunto de prueba específico).

A este tipo de algoritmos se les llama **aprendizaje supervisado**. La idea central es separar
datos para **entrenar** y datos para **evaluar** la capacidad de generalizar: predecir bien sobre
instancias que el modelo no ha visto.

Más adelante revisaremos el problema del **sobreajuste** (*overfitting*), que ocurre cuando el
modelo se ajusta demasiado a los datos de entrenamiento y pierde capacidad de generalización.

Algoritmos de inducción de árboles de decisión
----------------------------------------------

Veremos nuestro primer algoritmo de aprendizaje supervisado: la inducción de **árboles de decisión**.
Estos algoritmos son populares porque permiten interpretar (hasta cierto punto) el modelo generado.

Para entender el concepto, induciremos manualmente un árbol a partir del siguiente conjunto de datos:

.. list-table::
   :header-rows: 1
   :widths: 6 16 10 10 12

   * - id
     - hotel
     - estrellas
     - alberca
     - WiFi Gratis
   * - 1
     - Mariots
     - 2
     - Sí
     - Sí
   * - 2
     - Díaz Inn
     - 2
     - No
     - Sí
   * - 3
     - Mandarina
     - 5
     - Sí
     - No
   * - 4
     - Le Hotel
     - 3
     - Sí
     - No
   * - 5
     - Halton
     - 4
     - No
     - Sí
   * - 6
     - Tromp
     - 4
     - Sí
     - No

El objetivo es predecir si un hotel ofrece WiFi gratis. Un árbol de decisión evalúa una característica
en cada nodo, segmentando los objetos en grupos. Cada segmentación se define por una condición; al
cumplirse, seguimos una rama hasta llegar a una hoja que representa una categoría.

Al proponer el árbol manualmente, primero notamos que ``id`` y ``hotel`` son identificadores, no
características útiles para discriminar.

Si consideramos ``estrellas``, observamos que todos los hoteles de 5 estrellas (en este conjunto de
datos) no ofrecen WiFi gratis. Aunque esto podría no ser lógico en la vida real, el algoritmo solo
puede aprender de lo que está en los datos.

Luego observamos que (entre los restantes) si el hotel tiene alberca entonces no ofrece WiFi gratis.
El árbol propuesto sería:

.. figure:: _static/img/tree1.png
   :width: 70%
   :align: center
   :alt: Árbol de decisión propuesto

   Árbol de decisión propuesto.

Este árbol no logra 100% de exactitud: el hotel “Mariots” quedaría mal clasificado. Aun así, sirve
para entender el proceso. Por ejemplo, clasifiquemos el siguiente registro de prueba:

.. list-table::
   :header-rows: 1
   :widths: 6 16 10 10 12

   * - id
     - hotel
     - estrellas
     - alberca
     - WiFi Gratis
   * - 12
     - Bella-Gio
     - 4
     - Sí
     - ?

Iniciamos en el **nodo raíz** evaluando si ``estrellas = 5``. Como 4 es menor que 5, bajamos al
**nodo interno** ``alberca``. Como el hotel sí tiene alberca, llegamos al **nodo hoja** que indica
que **no** ofrece WiFi gratis.

.. figure:: _static/img/tree-2.png
   :width: 50%
   :align: center
   :alt: Ruta de decisión

   Ruta de disparo para clasificar el registro de prueba.

El resultado sería:

.. list-table::
   :header-rows: 1
   :widths: 6 16 10 10 12

   * - id
     - hotel
     - estrellas
     - alberca
     - WiFi Gratis
   * - 12
     - Bella-Gio
     - 4
     - Sí
     - No

Incluso en este ejemplo simple, podríamos proponer muchos árboles distintos. No es posible evaluar
todas las opciones porque el espacio de búsqueda es enorme. Por eso, necesitamos una **heurística**
que guíe la construcción del árbol.

La observación clave es que, al agregar un nodo, estamos dividiendo el conjunto de registros que
llegan a ese nodo en subgrupos. Buscamos que esos subgrupos sean lo más **homogéneos** posible con
respecto a la clase. A esta idea se le llama **pureza**.

Uno de los primeros algoritmos de inducción de árboles fue propuesto por Hunt en el trabajo
*Concept Learning System (CLS)*. Este trabajo es base de algoritmos como ID3, C4.5 y CART. Estos
algoritmos recursivos buscan dividir los datos en subgrupos cada vez más puros:

1. Para un nodo :math:`t`, consideramos el conjunto de registros :math:`D_t` que llegan al nodo.
   Si todos los registros en :math:`D_t` pertenecen a la misma clase :math:`y_t`, entonces :math:`t`
   es un **nodo hoja** y se le asigna la etiqueta :math:`y_t`.

2. Si en :math:`D_t` hay registros de más de una clase, seleccionamos una nueva **condición de
   selección** y agregamos nodos hijo (uno por condición). Luego repetimos el proceso de forma
   recursiva en cada hijo.

El componente importante es cómo seleccionar la condición de división. Se elige la que produzca
subgrupos más puros.

También deben considerarse casos especiales:

- Si para alguna rama el conjunto :math:`D_t` es vacío, podemos asignar la etiqueta más frecuente
  en el nodo padre.
- Si existen registros con los mismos valores de atributos pero con clases distintas, se puede
  crear un nodo hoja asignando la clase mayoritaria en :math:`D_t`.

Métricas de pureza
~~~~~~~~~~~~~~~~~~

Las métricas para evaluar la calidad de una división se basan en la pureza del conjunto antes y
después de dividir. Las más utilizadas son **entropía** y **Gini**.

Entropía:

.. math::

   \mathrm{Entropía}(D_t) = -\sum_{y \in Y} p(y)\,\log_2 p(y)

donde:

- :math:`D_t` es el conjunto de registros en el nodo,
- :math:`Y` es el conjunto de clases presentes en :math:`D_t`,
- :math:`p(y)` es la proporción de registros de clase :math:`y` en :math:`D_t`.

Coeficiente de Gini:

.. math::

   \mathrm{Gini}(D_t) = 1 - \sum_{y \in Y} [p(y)]^2

Ganancia de información
~~~~~~~~~~~~~~~~~~~~~~~

Tras calcular la impureza de los nodos hijo, se compara contra la impureza del nodo padre para
decidir si conviene dividir. En muchos algoritmos se utiliza una medida de **ganancia**
(o ganancia de información), :math:`\Delta`, que considera impureza antes y después de dividir:

.. math::

   \Delta = I(\text{padre}) - \sum_{j=1}^{k}\frac{N(h_j)}{N}\,I(h_j)

donde:

- :math:`I(\cdot)` es una medida de impureza (entropía o Gini),
- :math:`N` es el número de registros en el nodo padre,
- :math:`N(h_j)` es el número de registros en el hijo :math:`h_j`.

En algoritmos como ID3 se calcula :math:`\Delta` para cada atributo y se elige dividir con el que
tenga **mayor** ganancia.

Ejercicio
~~~~~~~~~

- Implementa (o utiliza una biblioteca) para probar un algoritmo de inducción de árboles de decisión
  en los conjuntos `Zoo <https://archive.ics.uci.edu/ml/datasets/Zoo>`_ y
  `SMS Spam Collection <https://archive.ics.uci.edu/ml/datasets/SMS+Spam+Collection>`_.

- Prueba distintas métricas de pureza (Gini y entropía).

- ¿El algoritmo tiene parámetros para ajustar qué tanto se dividen los nodos?

- ¿Cómo comparas el desempeño al cambiar parámetros?

La matriz de confusión
----------------------

Una **matriz de confusión** es una tabla que permite visualizar el desempeño de un clasificador.
En las filas se colocan las clases reales y en las columnas las clases predichas (manteniendo el
mismo orden de etiquetas en ambos ejes).

Por ejemplo, la siguiente figura muestra una matriz de confusión para un clasificador del conjunto
Iris:

.. figure:: _static/img/irisconf.png
   :width: 70%
   :align: center
   :alt: Matriz de confusión Iris

   Matriz de confusión de un clasificador para el conjunto Iris.

La interpretamos así:

- Sumando cada fila conocemos cuántos objetos reales hay por clase (en el ejemplo, 50 por clase).
- Un clasificador perfecto tendría 50 en cada celda de la diagonal y ceros en el resto.

En clasificación binaria (por ejemplo, *ham* vs *spam*) existen dos tipos de error:

- **Falso positivo (FP):** predecir *positivo* cuando el caso era *negativo*.
- **Falso negativo (FN):** predecir *negativo* cuando el caso era *positivo*.

En problemas médicos suele hablarse de:

- **VP (verdadero positivo):** se detecta correctamente la enfermedad.
- **VN (verdadero negativo):** se detecta correctamente la ausencia.
- **FP:** se diagnostica enfermedad cuando no la hay.
- **FN:** se diagnostica ausencia cuando sí hay enfermedad.

.. figure:: _static/img/tpconf.png
   :width: 70%
   :align: center
   :alt: Matriz de confusión binaria

   Matriz de confusión para un clasificador binario.

A partir de esta matriz se calculan métricas:

Exactitud (accuracy)
~~~~~~~~~~~~~~~~~~~~

.. math::

   \mathrm{Exactitud} = \frac{\mathrm{VP} + \mathrm{VN}}{\mathrm{VP} + \mathrm{VN} + \mathrm{FP} + \mathrm{FN}}

Sensibilidad (recall)
~~~~~~~~~~~~~~~~~~~~~

.. math::

   \mathrm{Sensibilidad} = \frac{\mathrm{VP}}{\mathrm{VP} + \mathrm{FN}}

Especificidad
~~~~~~~~~~~~~

.. math::

   \mathrm{Especificidad} = \frac{\mathrm{VN}}{\mathrm{VN} + \mathrm{FP}}

Precisión (precision)
~~~~~~~~~~~~~~~~~~~~~

.. math::

   \mathrm{Precisión} = \frac{\mathrm{VP}}{\mathrm{VP} + \mathrm{FP}}

Valor de predicción negativo
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. math::

   \mathrm{VPN} = \frac{\mathrm{VN}}{\mathrm{VN} + \mathrm{FN}}

F-score
~~~~~~~

El :math:`F_1` considera la media armónica entre precisión y sensibilidad:

.. math::

   F_1 = \frac{2}{\tfrac{1}{\mathrm{Sensibilidad}} + \tfrac{1}{\mathrm{Precisión}}}

Ejercicio
~~~~~~~~~

- Genera matrices de confusión para distintos parámetros de un algoritmo de clasificación.
- Calcula además exactitud, precisión y sensibilidad.

Sobreajuste de modelos
----------------------

Un problema común en aprendizaje supervisado es el **sobreentrenamiento** (*overfitting*). Sucede
cuando minimizamos demasiado el error de clasificación en entrenamiento o cuando generamos modelos
demasiado complejos.

.. figure:: _static/img/overfitting.png
   :width: 70%
   :align: center
   :alt: Ejemplo de sobreajuste

   Ejemplo gráfico de sobreajuste de modelos.

En (a) están los datos. En (b) el modelo aún no ajusta bien; intentamos ajustarlo más. En (c) se
muestra un ajuste razonable. En (d) el modelo se sobreajusta: no tiene error en entrenamiento, pero
es demasiado elaborado y generaliza peor.

La misma idea aparece en árboles de decisión: un árbol con muchos nodos requiere más “información”
para expresarse que uno pequeño.

La relación típica entre error de entrenamiento y error de prueba se ilustra a continuación:

.. figure:: _static/img/error.png
   :width: 70%
   :align: center
   :alt: Error vs entrenamiento

   Relación entre cantidad de entrenamiento y errores en prueba/entrenamiento.

Otros motivos de sobreajuste incluyen:

- Elección incorrecta del conjunto de entrenamiento (por ejemplo, tomar los primeros :math:`n`
  registros). En Iris, los datos suelen venir ordenados por clase, lo cual sesga el entrenamiento.
- Un conjunto de datos no representativo de la realidad.
- Ruido en los datos (etiquetas incorrectas, muchos valores atípicos, etc.).

Evaluando el desempeño de los clasificadores
--------------------------------------------

Al entrenar distintos modelos, debemos seleccionar el adecuado: ni demasiado simple ni demasiado
complejo. Para estimar el desempeño buscamos medirlo sobre datos de prueba de forma lo menos sesgada
posible. A continuación se describen métodos comunes basados en la selección de conjuntos de
entrenamiento y prueba.

Holdout
~~~~~~~

Este método divide los datos disponibles en dos subconjuntos complementarios. Por ejemplo, 70% para
entrenar y 30% para probar. Es sencillo y rápido, pero tiene desventajas:

1. Menos registros para entrenamiento.
2. Dependencia de la proporción elegida.
3. Riesgo de particiones desbalanceadas (por ejemplo, una clase sobrerrepresentada en entrenamiento).

Holdout con muestras aleatorias
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Para mitigar el sesgo, se repite el experimento varias veces con particiones aleatorias usando la
misma proporción. La estimación final es el promedio de los errores. Un problema: no se garantiza
cuántas veces aparece cada registro en entrenamiento o prueba.

Validación cruzada (*cross-validation*)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

La versión más común es la validación cruzada de :math:`k` particiones (*k-fold*). El conjunto se
divide en :math:`k` particiones del mismo tamaño. Se evalúa :math:`k` veces: cada vez se usa una
partición distinta para prueba y las otras :math:`k-1` para entrenamiento.

**Leave-one-out** es un caso extremo donde cada registro individual actúa como conjunto de prueba
y el resto como entrenamiento.

Métodos para comparar clasificadores
------------------------------------

Una tarea central es seleccionar el mejor clasificador para cierto conjunto de datos. Esto es
difícil porque, en ocasiones, los modelos no fueron entrenados con los mismos datos o no se conocen
las condiciones exactas de evaluación.

Se utilizan pruebas estadísticas para evaluar la hipótesis de que un clasificador es mejor que otro.
Hay que tener cuidado: algunas pruebas tienen alta probabilidad de cometer error Tipo I (concluir que
hay diferencia cuando no la hay).

Al diseñar comparaciones debemos considerar fuentes de variación:

1. Variación aleatoria en la selección de datos de prueba.
2. Variación por la elección de datos de entrenamiento.
3. Algoritmos no deterministas (por ejemplo, redes neuronales que inician en estados aleatorios).

Algunas pruebas utilizadas:

1. **McNemar** usando los mismos datos para ambos clasificadores.
2. Comparaciones sobre errores de validación cruzada (por ejemplo, con :math:`k` suficientemente
   grande) y pruebas :math:`t` sobre la diferencia de errores.

.. _clasificacion_basica_referencia_ejemplos:

Referencia a ejemplos del libro de Python
-----------------------------------------

Como los ejemplos prácticos (entrenar un clasificador, partición *train/test*, matriz de confusión,
métricas y validación cruzada con ``scikit-learn``) ya están desarrollados en el libro de Python,
aquí conviene **evitar duplicarlos** y, en su lugar, hacer una referencia cruzada.

.. important

   En este capítulo nos enfocamos en la intuición, definiciones y criterios de evaluación.
   Para la implementación en Python (``train_test_split``, ``confusion_matrix``,
   ``classification_report``, *k-fold cross validation*), consulta el libro de Python:
   :ref:`<ETIQUETA_DEL_LIBRO_PYTHON>`.

Sugerencia de integración (en el lugar donde iba el ejemplo)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Reemplaza el encabezado “Ejemplo de una matriz de confusión” (y/o el ejemplo de evaluación) por
un bloque como el siguiente:

.. seealso::

   Implementación en Python (``scikit-learn``):
   .

   En esa sección se muestra cómo:

   - dividir datos en entrenamiento y prueba,
   - entrenar un modelo,
   - generar la matriz de confusión,
   - calcular *accuracy*, *precision*, *recall* y :math:`F_1`,
   - aplicar validación cruzada y comparar configuraciones.




