Exploración de los Datos
========================

Estadística descriptiva
-----------------------

Para darnos una mejor idea de la naturaleza de los fenómenos capturados por los datos,
debemos explorarlos. En esta sección conoceremos algunas de las técnicas más populares
para explorar conjuntos de datos.

Como primera herramienta tenemos la **estadística descriptiva**, la cual nos permite
organizar, presentar y describir conjuntos de datos. También contamos con herramientas
de apoyo como gráficas, tablas e incluso animaciones, las cuales complementan las
medidas numéricas. Una herramienta muy importante, entonces, es la **visualización**:
convertir los datos a formatos visuales para resaltar características y relaciones
entre los objetos.

Un ejemplo clásico que resalta la necesidad de visualizar los datos es el ejercicio
publicado por `autodeskresearch.com <https://www.autodeskresearch.com/publications/samestats>`_.
Ahí se presentan varios conjuntos de datos que comparten medidas estadísticas, pero
tienen gráficas muy distintas.

Antes de elegir herramientas de análisis (o incluso el tipo de preprocesamiento), es
recomendable hacer una exploración preliminar. No olvidemos que los humanos somos
buenos reconociendo patrones; así que antes de aplicar toda la maquinaria conviene
“echar un vistazo”. Es común encontrar indicios que nos ayuden a elegir mejor las
técnicas posteriores.

En esta sección nos enfocaremos en:

- Parámetros estadísticos
- Visualización
- Herramientas OLAP (*Online Analytical Processing*)

Parámetros estadísticos
~~~~~~~~~~~~~~~~~~~~~~~

Cuando analizamos una gran cantidad de objetos (por ejemplo, alumnos de una escuela o
la población de un país), utilizamos números que resumen características de dichos
objetos. Por ejemplo, al comparar edades entre dos países podríamos hablar de la
**edad promedio**.

En este caso, la edad es una **variable estadística** y la media aritmética es un
**parámetro estadístico**. Los parámetros se obtienen aplicando cálculos al conjunto
de datos. Como vimos antes, el tipo de cálculo posible depende del nivel de medición:
hay operaciones válidas con variables continuas, pero no con variables categóricas.

Medidas de posición
^^^^^^^^^^^^^^^^^^^

Estas medidas identifican valores “representativos” dentro del rango observado.

Media
"""""

La media (o media aritmética) se calcula sumando todos los valores y dividiendo entre
el número total de observaciones:

.. math::

   \bar{x} = \frac{1}{n}\sum_{i=1}^{n} x_i

Cuando los datos son muy variados o existen valores atípicos, la media puede ser poco
representativa. Por ejemplo, si las edades son:

``[10, 10, 90, 10, 10]``

la media es:

.. math::

   \frac{10 + 10 + 90 + 10 + 10}{5} = 26

pero 26 años no describe bien al grupo. En cambio, para:

``[12, 11, 9, 10, 10]``

la media es 10.4, mucho más representativa. En presencia de valores atípicos, la
**mediana** suele ser una mejor opción.

Mediana
"""""""

Para calcular la mediana, ordenamos los valores de menor a mayor. La mediana es el
valor central. Si el número de observaciones es par, la mediana es el promedio de los
dos valores centrales.

Ejemplo con número impar:

- Datos: ``[10, 10, 90, 10, 10]``
- Ordenados: ``[10, 10, 10, 10, 90]``
- Mediana: ``10``

Ejemplo con número par:

- Datos: ``[10, 12, 24, 90]``
- Mediana: promedio de 12 y 24, es decir, 18.

Formalmente, sea :math:`n` el número de elementos ordenados en forma creciente:

.. math::

   x_1, x_2, x_3, \ldots, x_n

- Si :math:`n` es impar, la mediana es :math:`x_{(n+1)/2}`.
- Si :math:`n` es par, la mediana es:

.. math::

   \frac{x_{n/2} + x_{(n/2)+1}}{2}

Moda
""""

En variables cualitativas no podemos calcular media o mediana (en el sentido numérico),
pero sí podemos contar frecuencias. La **moda** es el valor que aparece con mayor
frecuencia.

Medidas de dispersión
^^^^^^^^^^^^^^^^^^^^^

Estas medidas resumen qué tan “separados” o “variables” son los valores.

Varianza
""""""""

Para medir qué tanto varían los valores, tomamos como referencia la media. Para cada
valor calculamos su desviación respecto a la media y promediamos los cuadrados (para
evitar cancelación por signos):

.. math::

   \sigma_n^2 = \frac{1}{n}\sum_{i=1}^{n}\left(x_i - \overline{x}\right)^2

donde:

- :math:`x_i` es el dato en la posición :math:`i`,
- :math:`\overline{x}` es la media,
- :math:`n` es el número de datos.

La varianza queda en unidades al cuadrado. Por eso, al tomar la raíz cuadrada obtenemos
la **desviación estándar**, que se expresa en las mismas unidades de la variable.

Al igual que la media, varianza y desviación estándar son sensibles a valores atípicos.

Desviación absoluta promedio
""""""""""""""""""""""""""""

En lugar de elevar al cuadrado, podemos usar el valor absoluto de la desviación:

.. math::

   \frac{1}{n}\sum_{i=1}^{n}\left|x_i - m(X)\right|

(En este contexto, :math:`m(X)` denota un valor de referencia, típicamente la media.)

Desviación absoluta media (MAD)
"""""""""""""""""""""""""""""""

La desviación absoluta media o **MAD** (*Median Absolute Deviation*) es una medida robusta:
resiste mejor los valores atípicos. En lugar de usar la media, utiliza la mediana:

.. math::

   MAD(x) = \operatorname{mediana}\left(\left|x_i - \operatorname{mediana}(x)\right|\right)

Visualización
-------------

Los datos representan objetos del mundo real, los cuales no son entidades aisladas ni estáticas:
se relacionan entre sí, son dinámicos y pueden tener muchos atributos.

Pensemos, por ejemplo, en una base de datos de películas como `IMDb <https://www.imdb.com/>`_.
Los objetos principales son las películas, pero se relacionan con actores, críticos, reseñas de
usuarios, etc. Además, las películas tienen “historia”: *trailers*, fecha de estreno, ingresos
semanales por país, disponibilidad en formatos, popularidad en el tiempo, entre otros.

Visualizar estos datos puede ayudarnos a encontrar patrones interesantes y tomar decisiones. Por
ejemplo, si queremos decidir qué película ver hoy, podemos consultar una tabla con estrenos de
la semana:

.. figure:: _static/img/boxoffice.png
   :width: 70%
   :align: center
   :alt: Tabla de películas en cartelera

   En cartelera.

En una aplicación web, al seleccionar una película podemos ver más detalles:

.. figure:: _static/img/quiet.png
   :width: 70%
   :align: center
   :alt: Detalles de una película

   Detalles de la película.

Ignorar detalles en un primer paso (y luego profundizar solo en lo relevante) facilita la toma
de decisiones.

A veces necesitamos comparar películas con base en algunos atributos; por ejemplo, ver cuáles han
sido las más taquilleras:

.. figure:: _static/img/gross_mojo.png
   :width: 70%
   :align: center
   :alt: Recaudación neta en Estados Unidos

   Recaudación neta en Estados Unidos.

Para analizar relaciones entre variables, podemos abstraer aún más: representar cada película como
un punto y observar patrones (por ejemplo, presupuesto vs recaudación):

.. figure:: _static/img/pres_pelis_log.png
   :width: 70%
   :align: center
   :alt: Presupuesto vs recaudación en escala logarítmica

   Presupuesto vs recaudación (escala logarítmica).

En ocasiones también necesitamos muestrear datos: aun siendo “solo puntos”, visualizar enormes
cantidades puede ser costoso o la técnica podría no ser adecuada para toda la población.

.. warning::

   La visualización es una herramienta auxiliar, no un sustituto de la estadística ni del análisis.
   Una gráfica mal diseñada puede interpretarse incorrectamente o incluso engañar.

El tema es amplio y se relaciona con áreas como infografía, visualización científica, visualización
interactiva de datos y diseño gráfico.

.. figure:: _static/img/pres_pelis.png
   :width: 70%
   :align: center
   :alt: Presupuesto vs recaudación sin escala logarítmica

   Presupuesto vs recaudación (sin escala logarítmica).

Gráficas utilizadas en visualización de datos
---------------------------------------------

Gráfica de barras
~~~~~~~~~~~~~~~~~

La gráfica de barras es una forma muy popular de presentar valores. Cada barra tiene un tamaño
proporcional a la cantidad representada. Las barras pueden organizarse horizontal o verticalmente.
Cuando se muestran varios resultados, es común agrupar barras y diferenciarlas con colores.

.. figure:: _static/img/wc_barras.png
   :width: 70%
   :align: center
   :alt: Asistencia a las Copas del Mundo de la FIFA

   Asistencia a las Copas del Mundo de la FIFA.

Histograma
~~~~~~~~~~

Un histograma también utiliza barras, pero en este caso representan la **frecuencia** de los datos
en rangos de valores. Permite ver tendencias de una distribución. Cambiando el ancho de las barras
(*bins*) obtenemos distintas resoluciones.

Ejemplo (valores discretos): distribución de goles por partido en Copas del Mundo.

.. figure:: _static/img/histograma_goles.png
   :width: 70%
   :align: center
   :alt: Histograma de goles por partido

   Goles por partido en Copas del Mundo.

Ejemplo (valores continuos): distribución normal (por ejemplo, IQ de una población).

.. figure:: _static/img/histograma.png
   :width: 70%
   :align: center
   :alt: Histograma de datos continuos

   Histograma de datos continuos.

Diagrama de dispersión
~~~~~~~~~~~~~~~~~~~~~~

Se utiliza para ver la relación entre dos variables (o más). En 2D, cada punto se ubica por dos
coordenadas. Para representar variables adicionales podemos usar color, tamaño o forma.

En el ejemplo se relacionan tres variables: peso, millas por galón y caballos de fuerza; estos
últimos se representan con un mapa de color.

.. figure:: _static/img/cmap.png
   :width: 70%
   :align: center
   :alt: Dispersión con mapa de color

   Diagrama de dispersión mostrando la relación entre tres variables.

Gráfica de cajas
~~~~~~~~~~~~~~~~

La gráfica de cajas resume una distribución con cuartiles. La caja va del percentil 25% al 75% y
la línea central marca la mediana. Los “bigotes” representan rangos adicionales y los valores
atípicos suelen mostrarse como puntos.

Ejemplo: goles por partido en Copas del Mundo.

.. figure:: _static/img/boxgoles.png
   :width: 70%
   :align: center
   :alt: Boxplot de goles por partido

   Goles por partido en Copas del Mundo.

También es común comparar desempeño de procesos. Por ejemplo, rendimiento MPG por país de origen:

.. figure:: _static/img/mpg_box.png
   :width: 70%
   :align: center
   :alt: Boxplot de mpg por país

   Rendimiento en MPG por país de origen.

Coordenadas paralelas
~~~~~~~~~~~~~~~~~~~~~

Las coordenadas paralelas relacionan múltiples variables y sirven para observar patrones entre
dimensiones.

.. figure:: _static/img/andrews.png
   :width: 70%
   :align: center
   :alt: Andrews curves para Iris

   Representación multivariable del conjunto Iris (Andrews curves).

.. figure:: _static/img/paralelas_iris.png
   :width: 70%
   :align: center
   :alt: Coordenadas paralelas para Iris

   Coordenadas paralelas para el conjunto Iris.

Caras de Chernoff
~~~~~~~~~~~~~~~~~

Las caras de Chernoff representan múltiples variables usando rasgos faciales cuyo tamaño es
proporcional a los valores de distintas variables.

.. figure:: _static/img/Chernoff_faces.png
   :width: 70%
   :align: center
   :alt: Caras de Chernoff

   Caras de Chernoff.

Lectura adicional
-----------------

- *The Visual Display of Quantitative Information*, Edward R. Tufte (2001).
- *Fundamentals of Data Visualization*, Claus O. Wilke (en línea): http://serialmentor.com/dataviz/

OLAP y análisis de datos multidimensional
-----------------------------------------

Cuando hablamos de extracción de conocimiento en bases de datos, a veces pensamos en técnicas
avanzadas para consultar bases de datos relacionales usando SQL y generar reportes y estadísticas.
Estas actividades están más relacionadas con Inteligencia de Negocios (BI, *Business Intelligence*),
que se enfoca en reportes de datos históricos de operaciones empresariales.

Estas tareas aparecen en sistemas de apoyo a la toma de decisiones (DSS, *Decision Support Systems*)
y, sobre todo, aplicaciones de OLAP (*Online Analytical Processing*). OLAP se relaciona con consultas
a grandes volúmenes de datos, realizando operaciones de agregación sobre **dimensiones**.

Por ejemplo, un ejecutivo podría querer ver las ventas de cierta categoría de producto en el primer
trimestre del año en el estado de Nueva York. En este caso tenemos datos agregados (ventas) sobre:

- categoría de producto,
- tiempo (primer trimestre),
- dimensión geográfica (Nueva York).

El mismo ejecutivo podría pedir la misma consulta para todo el año o solo el segundo semestre.
A estas consultas se les conoce como análisis multidimensional y se realizan sobre datos históricos
almacenados en almacenes de datos.

En esta sección daremos un vistazo a estas tecnologías porque pueden ser complementarias a técnicas
de minería de datos. Empezaremos por revisar almacenes de datos.

Almacenes de datos
~~~~~~~~~~~~~~~~~~

Un almacén de datos (DW, *Data Warehouse*) es un repositorio donde se almacenan principalmente datos
históricos provenientes de distintas fuentes con el objetivo de alimentar aplicaciones OLAP y
reportes para la toma de decisiones.

Como los datos provienen de sistemas externos, cargar y mantener el DW actualizado representa un
desafío. A este proceso se le llama **ETL** (*Extract, Transform, Load*).

Aunque podemos implementar un DW sobre un SGBD (por ejemplo, Oracle o PostgreSQL), el diseño no sigue
las reglas típicas de normalización para transacciones. El objetivo principal es ejecutar consultas
multidimensionales con rapidez. Por eso se utilizan esquemas **estrella** o **copo de nieve**.

Data Marts
^^^^^^^^^^

Los *Data Marts* son almacenes intermedios que no incluyen toda la información de una empresa.
Suelen especializarse en una funcionalidad (por ejemplo, ventas o contabilidad). Su ventaja es que
requieren menos recursos de extracción y mantenimiento.

Data Lakes
^^^^^^^^^^

Un *Data Lake* es un repositorio de **datos crudos** en su formato original; no necesariamente
impone un esquema rígido de atributos o metadatos. Puede contener imágenes, textos, PDFs, lecturas
de sensores, etc. Este tipo de almacenes se usa comúnmente como fuente para minería de datos.

El cubo OLAP
~~~~~~~~~~~~

Para realizar operaciones OLAP tipo cubo, es común organizar la información en un esquema estrella:

.. figure:: _static/img/star.png
   :width: 70%
   :align: center
   :alt: Diseño estrella

   Diseño estrella.

El esquema estrella distingue dos tipos de tablas:

1. **Hechos.**
   Una sola tabla que registra el evento a analizar (por ejemplo, ventas). Un punto clave es la
   **granularidad**: ¿almacenamos cada venta individual o ventas agregadas por producto y día?
   Esta decisión afecta directamente el tamaño del almacén.

2. **Dimensiones.**
   Tablas con las dimensiones por las que se consultará (tiempo, producto, tienda, región, etc.).
   No se normalizan estrictamente; se prefiere duplicar información antes que ejecutar *joins*
   costosos. Las dimensiones suelen tener jerarquías, por ejemplo:
   ``Colonia > Ciudad > Estado > País``.

Podemos visualizar el esquema como un cubo, donde cada cara corresponde a una dimensión.

.. figure:: _static/img/cubo.png
   :width: 70%
   :align: center
   :alt: Cubo OLAP

   Cubo OLAP para un almacén de ventas.

Sobre el cubo se realizan operaciones típicas:

- **Drill-down:** mayor detalle en una dimensión (por ejemplo, de estado a ciudad).
- **Roll-up:** agregar datos subiendo nivel en la jerarquía (de ciudad a estado).
- **Slice:** seleccionar una “rebanada” del cubo (una cara) para obtener una tabla.
- **Dice:** seleccionar rangos en dos o más dimensiones para extraer un subcubo.

.. figure:: _static/img/roll.png
   :width: 70%
   :align: center
   :alt: Operación drill-down y roll-up

   Operación *drill-down* (y relación con *roll-up*).

