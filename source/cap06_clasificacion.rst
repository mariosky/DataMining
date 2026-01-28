Otras técnicas de clasificación
===============================

Clasificadores basados en reglas
---------------------------------

En la sección anterior, el modelo utilizado para clasificar se representaba como un **árbol de decisión**.
Los clasificadores que veremos a continuación representan al modelo como un conjunto de reglas **IF-THEN**.
Estas reglas son similares a las condiciones de los nodos de un árbol. Por ejemplo, una regla para
clasificar hoteles podría ser:

.. code-block:: text

   Regla 1: IF alberca = Sí THEN WiFiGratis = Sí.

Las reglas tienen dos partes:

1. El **antecedente** o precondición (**IF**), que contiene expresiones condicionales sobre los atributos
   y, de manera opcional, operadores lógicos (por ejemplo, ``alberca = Sí OR estrellas < 5``).

2. El **consecuente** (**THEN**), donde se expresa la predicción de la clase o categoría del objeto.

Ejemplo
~~~~~~~

Recordemos el ejemplo de los hoteles:

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

Un modelo basado en reglas para clasificar hoteles puede ser:

.. code-block:: text

   IF estrellas = 5 OR estrellas = 3 THEN WiFiGratis = Sí
   IF alberca = No THEN WiFiGratis = Sí
   IF alberca = Sí THEN WiFiGratis = No

Los algoritmos clasificadores basados en reglas extraen reglas a partir de los datos mediante, por ejemplo:

1. Extracción de reglas desde árboles de decisión. Las rutas de la raíz a las hojas forman los antecedentes
   y las hojas forman los consecuentes. :cite:`han2011data`

2. Generación directa de reglas desde los datos mediante algoritmos de **cobertura** (covering),
   como RIPPER :cite:`cohen1995fast` o CN2 :cite:`clark1989cn2`.


`k` vecinos más próximos
-------------------------

Este algoritmo se basa en una idea sencilla: asignar al objeto a clasificar la clase que tengan los
objetos del conjunto de entrenamiento que más se parezcan a él.

Para calcular la similaridad entre dos objetos se puede usar, en el caso continuo, la **distancia euclidiana**
entre sus vectores de características. El parámetro :math:`k` especifica cuántos vecinos (ordenados por
similaridad) se consideran para asignar la clase. En el caso más sencillo se asigna la clase **mayoritaria**
entre los :math:`k` vecinos.

El método es fácil de implementar, pero la elección de :math:`k` puede afectar el desempeño:

- Si :math:`k` es muy pequeño, el clasificador puede volverse sensible al ruido y a valores atípicos.
- Si :math:`k` es muy grande, tenderá a “promediar” regiones con clases distintas.

Además, el voto de cada vecino puede ponderarse con respecto a la distancia (por ejemplo, dando más peso
a vecinos más cercanos).

.. figure:: _static/img/kvalores.png
   :width: 80%
   :align: center
   :alt: Efecto de k en kNN

   Efecto de la elección del número de vecinos :math:`k`.


Naïve Bayes
-----------

Este clasificador recibe el nombre de *Naïve* (en español, *ingenuo*) porque asume que los atributos son
**independientes condicionalmente** dada la clase. Es fácil ver que esta suposición no siempre se cumple.
Por ejemplo, para los atributos de un hotel *número de estrellas* y *alberca*, podríamos imaginar que un
hotel de 4 o 5 estrellas es más probable que tenga alberca, mientras que uno de 2 estrellas quizá no.

Naïve Bayes considera que, para una clase :math:`C` y un objeto con atributos
:math:`\{A_1, A_2, \dots, A_n\}`, podemos calcular:

.. math::

   P(C \mid A_1, A_2, \dots, A_n)

Para clasificar un registro se calcula esta probabilidad para cada clase :math:`C_j` y se elige la mayor.
Aplicando Bayes:

.. math::

   P(C_j \mid A_1, A_2, \dots, A_n) =
   \frac{P(A_1, A_2, \dots, A_n \mid C_j)\,P(C_j)}
        {P(A_1, A_2, \dots, A_n)}

Como el denominador es común para todas las clases, basta con comparar:

.. math::

   P(A_1, A_2, \dots, A_n \mid C_j)\,P(C_j)

Y si asumimos independencia condicional:

.. math::

   P(A_1, A_2, \dots, A_n \mid C_j) =
   \prod_{i=1}^{n} P(A_i \mid C_j)

Ejemplo
~~~~~~~

Como ejemplo, usemos un fragmento del conjunto de datos de
`Acute Inflammations <https://archive.ics.uci.edu/ml/datasets/Acute+Inflammations>`_
y un clasificador Naïve Bayes para determinar si un paciente tiene una inflamación aguda.

Datos:

.. list-table::
   :header-rows: 1
   :widths: 8 8 14 18 14 18 10

   * - temp
     - nausea
     - dolor lumbar
     - necesidad constante
     - dolor al orinar
     - comezón en la uretra
     - infección
   * - 35.5
     - no
     - yes
     - no
     - no
     - no
     - no
   * - 36.0
     - no
     - yes
     - no
     - no
     - no
     - no
   * - 36.8
     - no
     - no
     - yes
     - yes
     - yes
     - yes
   * - 37.0
     - no
     - no
     - yes
     - yes
     - yes
     - yes
   * - 37.4
     - no
     - no
     - yes
     - no
     - no
     - yes
   * - 37.1
     - no
     - no
     - yes
     - no
     - no
     - yes
   * - 37.6
     - no
     - no
     - yes
     - yes
     - no
     - yes
   * - 37.8
     - no
     - no
     - yes
     - yes
     - yes
     - yes
   * - 38.0
     - no
     - yes
     - yes
     - no
     - yes
     - no
   * - 39.0
     - no
     - yes
     - yes
     - no
     - yes
     - no
   * - 40.4
     - yes
     - yes
     - no
     - yes
     - no
     - no
   * - 40.8
     - no
     - yes
     - yes
     - no
     - yes
     - no
   * - 41.5
     - yes
     - yes
     - no
     - yes
     - no
     - no
   * - 41.5
     - no
     - yes
     - yes
     - no
     - yes
     - no

Paciente:

.. list-table::
   :header-rows: 1
   :widths: 8 8 14 18 14 18 10

   * - temp
     - nausea
     - dolor lumbar
     - necesidad constante
     - dolor al orinar
     - comezón en la uretra
     - infección
   * - 36.6
     - no
     - no
     - yes
     - yes
     - yes
     - ?

Como primer paso calculamos la probabilidad a priori :math:`P(C_j)`:

.. math::

   P(C_j) = \frac{N_{C_j}}{N}

Por ejemplo (con :math:`N=14`):

.. math::

   P(\text{``yes''}) = \frac{6}{14} \approx 0.429,\qquad
   P(\text{``no''}) = \frac{8}{14} \approx 0.571

Para atributos discretos:

.. math::

   P(A_i=v \mid C_k) = \frac{\lvert A_i=v \;\wedge\; C_k\rvert}{N_{C_k}}

donde:

- :math:`\lvert A_i=v \;\wedge\; C_k\rvert` es el número de registros con :math:`A_i=v` que pertenecen a la clase :math:`C_k`,
- :math:`N_{C_k}` es el número de registros en la clase :math:`C_k`.

En la siguiente tabla se muestran probabilidades estimadas para atributos discretos (como ejemplo):

.. list-table::
   :header-rows: 1
   :widths: 8 10 14 18 14 18

   * - clase
     - nausea
     - dolor lumbar
     - necesidad constante
     - dolor al orinar
     - comezón en la uretra
   * - yes
     - 0.14
     - 0.57
     - 0.71
     - 0.43
     - 0.50
   * - no
     - 0.86
     - 0.43
     - 0.29
     - 0.57
     - 0.50

Para el caso de atributos continuos existen varias opciones :cite:`tan2007introduction`:

- Discretizar el rango creando particiones.
- Hacer una división binaria con respecto a un umbral.
- Estimar una densidad de probabilidad, típicamente asumiendo una normal y estimando sus parámetros.

En este ejemplo, ``temp`` es continuo. Si asumimos una distribución normal por clase:

.. list-table::
   :header-rows: 1
   :widths: 8 10 10 22

   * - clase
     - media
     - stdev
     - densidad normal en :math:`x=36.6`
   * - yes
     - 37.28
     - 2.38
     - 0.16
   * - no
     - 39.09
     - 0.34
     - 0.00

.. note::

   Si alguna probabilidad (o densidad) se vuelve cero, el producto completo se colapsa.
   En implementaciones prácticas se usa suavizado (por ejemplo, *Laplace smoothing*) o
   trabajo en log-espacio para evitar problemas numéricos.

Con estas estimaciones (a manera ilustrativa):

.. math::

   P(\text{Paciente}\mid \text{no}) =
   0.86 \cdot 0.43 \cdot 0.29 \cdot 0.57 \cdot 0.50 \cdot 0.16

.. math::

   P(\text{Paciente}\mid \text{yes}) =
   0.14 \cdot 0.57 \cdot 0.71 \cdot 0.43 \cdot 0.50 \cdot 0.00 = 0

Y al ponderar por los priors:

.. math::

   P(\text{Paciente}\mid \text{no})P(\text{no}) \quad\text{vs}\quad
   P(\text{Paciente}\mid \text{yes})P(\text{yes})

Por lo tanto, con estas cifras, la clase predicha sería **``no``**.


Redes neuronales artificiales
-----------------------------

Las técnicas de clasificación que exploraremos en esta sección —redes neuronales artificiales y
máquinas de vectores de soporte— comparten una idea general: encontrar funciones que definan una
**frontera de decisión**.

En un problema binario, buscamos una función que tome como entrada un vector de características y lo
asigne a una de dos clases. Para entender esta idea, empecemos con un caso sencillo: conjuntos de
datos **linealmente separables**.

Un conjunto es linealmente separable si es posible clasificarlo trazando una recta (en 2D) que separe
clases. En general, hablamos de **hiperplanos** de dimensión :math:`D-1` en un espacio :math:`D`.
En 2D el hiperplano es una recta; en 3D es un plano; y así sucesivamente.

Una manera de modelar este tipo de problemas es empleando **funciones discriminantes**.

Funciones discriminantes
~~~~~~~~~~~~~~~~~~~~~~~~

Un discriminante es una función que toma un vector como entrada y le asigna una clase. Nos
concentraremos en discriminantes lineales (su frontera de decisión es un hiperplano) y, por ahora,
en clasificación binaria.

Una función discriminante lineal típica es:

.. math::

   y(\vec{x}) = \vec{w}^T\vec{x} + w_0

donde :math:`\vec{w}` es el vector de pesos y :math:`w_0` es el sesgo (a :math:`-w_0` también se le
llama umbral). Si tenemos :math:`C=\{c_1,c_2\}`, podemos decidir:

- si :math:`y(\vec{x}) \ge 0`, asignar :math:`c_1`;
- en caso contrario, asignar :math:`c_2`.

.. figure:: _static/img/d1.png
   :width: 70%
   :align: center
   :alt: Discriminante en 2D

   Objetos y función discriminante en 2D.

El hiperplano :math:`y(\vec{x})=0` está determinado por :math:`\vec{w}` y todos los vectores ortogonales
a él. El sesgo :math:`w_0` desplaza el hiperplano fuera del origen.

.. figure:: _static/img/d2.png
   :width: 70%
   :align: center
   :alt: w y vectores ortogonales

   Función discriminante definida por :math:`\vec{w}` y vectores ortogonales.

.. figure:: _static/img/d3.png
   :width: 70%
   :align: center
   :alt: Hiperplano con sesgo

   Función discriminante definida por :math:`\vec{w}` y un sesgo :math:`w_0`.

El objetivo del entrenamiento es resolver un problema de optimización: encontrar parámetros
:math:`\vec{w}` y :math:`w_0` que minimicen el error de clasificación en el conjunto de entrenamiento.

El perceptrón
~~~~~~~~~~~~~

Un discriminante lineal de gran importancia histórica es el **perceptrón** :cite:`rosenblatt1962principles`.
Aunque el perceptrón solo puede clasificar conjuntos linealmente separables, su desarrollo sentó las bases
de las redes neuronales artificiales.

.. figure:: _static/img/perceptron.png
   :width: 70%
   :align: center
   :alt: Perceptrón

   Componentes básicos del perceptrón.

La salida lineal pasa por una función de activación. En el caso básico (función escalón):

.. math::

   f(a)=
   \begin{cases}
     +1, & a \ge 0 \\
     -1, & a < 0
   \end{cases}

Con esto, el objetivo :math:`t` también se expresa como :math:`t \in \{-1, +1\}`.

Criterio de error
^^^^^^^^^^^^^^^^^

Un criterio clásico del perceptrón es sumar el aporte de los objetos mal clasificados (conjunto :math:`M`):

.. math::

   E_P(\vec{w}) = -\sum_{n \in M} \vec{w}^T \phi(\vec{x}_n)\,t_n

donde :math:`\phi(\cdot)` es una transformación fija (a veces se incluye un término constante para el sesgo).

Algoritmo de aprendizaje
^^^^^^^^^^^^^^^^^^^^^^^^

Para ajustar los pesos se puede usar **gradiente descendente estocástico** (SGD). La actualización típica es:

.. math::

   \vec{w}^{(\tau+1)} = \vec{w}^{(\tau)} + \eta\,\phi(\vec{x}_n)\,t_n

donde :math:`\eta` es la tasa de aprendizaje y :math:`\tau` indexa la iteración.

Red neuronal artificial multicapa
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Un perceptrón por sí solo no puede resolver problemas como XOR. Este caso sí puede modelarse con una red
**multicapa** (*Multi-Layer Perceptron*, MLP). Una MLP incluye al menos una capa oculta con funciones de
activación no lineales.

.. figure:: _static/img/nnml.png
   :width: 70%
   :align: center
   :alt: Red multicapa

   Red neuronal artificial multicapa.

El entrenamiento en MLP se realiza típicamente con *backpropagation* y variantes de gradiente descendente.


Máquinas de vectores de soporte
-------------------------------

.. figure:: _static/img/svn_idea.png
   :width: 70%
   :align: center
   :alt: Idea del margen máximo

   Dos opciones para el camino que pasa por el jardín.

El algoritmo de **Support Vector Machines** (SVM) utiliza un discriminante lineal, pero agrega una
restricción: la frontera debe colocarse para **maximizar el margen**.

La intuición es que un margen mayor tiende a producir un clasificador más robusto, ya que evita una
frontera “demasiado pegada” a los datos de entrenamiento.

El caso linealmente separable
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. figure:: _static/img/svm.png
   :width: 70%
   :align: center
   :alt: Vectores de soporte

   Vectores de soporte y margen.

Partimos de una función discriminante lineal:

.. math::

   y(\vec{x}) = \vec{w}^T\vec{x} + w_0

En la literatura de SVM :cite:`cortes1995support` al sesgo se le suele llamar :math:`b`, así que reescribimos:

.. math::

   y(\vec{x}) = \vec{w} \cdot \vec{x} + b

Los hiperplanos que delimitan el margen se expresan como:

.. math::

   \vec{w}^T\vec{x} + b = 1, \qquad
   \vec{w}^T\vec{x} + b = -1

El margen resulta:

.. math::

   \frac{2}{\lVert \vec{w} \rVert} =
   \frac{2}{\sqrt{\vec{w}\cdot \vec{w}}}

Así, el hiperplano óptimo es el que **minimiza** :math:`\vec{w}\cdot\vec{w}` sujeto a:

.. math::

   y_i(\vec{w}^T\vec{x}_i + b) \ge 1,\qquad i=1,\dots,N

Este es un problema de optimización (programación cuadrática) y su solución involucra multiplicadores de
Lagrange. Los puntos más cercanos a la frontera que determinan el margen se conocen como **vectores de soporte**.


Métodos ensamble
----------------

Cuando vamos a tomar una decisión difícil —por ejemplo, un diagnóstico médico delicado o el rumbo de una
empresa— suele ser mejor consultar a varios expertos. En minería de datos, una idea análoga es combinar
varios modelos: a esto se le llama un método **ensamble** (*ensemble*).

En un ensamble, varios clasificadores se entrenan (a menudo) con diferentes muestras o configuraciones.
Después, al predecir, se combinan las decisiones mediante votación o promedios.

.. figure:: _static/img/ensemble.png
   :width: 70%
   :align: center
   :alt: Esquema de ensamble

   Esquema general del aprendizaje ensamble.

A continuación se describen dos métodos ensamble populares.

Bagging
~~~~~~~

Bagging (*Bootstrap Aggregating*) :cite:`breiman1996bagging` consiste en tomar :math:`n` muestras con
reemplazo del conjunto de entrenamiento. A estas muestras se les conoce como **bootstrap** y se usan
para entrenar :math:`n` clasificadores. Al predecir, los resultados se promedian o se usan como votación.

Boosting
~~~~~~~~

En Boosting, las muestras se toman de manera secuencial. Se entrena un clasificador y se registran los
objetos mal clasificados. Luego se sesga la siguiente muestra hacia esos objetos para entrenar un
clasificador que “corrija” al anterior. Esto se repite :math:`n` veces.

AdaBoost (*Adaptive Boosting*) :cite:`freund1997decision` es una implementación popular: al combinar
clasificadores, pondera sus predicciones de acuerdo con su desempeño.

