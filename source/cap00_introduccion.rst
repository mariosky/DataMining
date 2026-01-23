Introducción
============

Esta sección tiene como objetivo darte una visión general del tema central de este
libro: la **extracción de conocimiento a partir de grandes cantidades de datos**.
Como tal vez te imagines, esta no es una tarea sencilla, ya que involucra distintas
áreas del conocimiento científico y un buen número de tecnologías.

No te preocupes: vamos a comenzar revisando los conceptos básicos, procurando que
tengas una buena idea de **dónde**, **cuándo** y **por qué** aplicar estas técnicas.

Al final de este capítulo podrás responder preguntas como:

- ¿Qué buscamos en los datos?
- ¿Cuál es el proceso para el descubrimiento de conocimiento en bases de datos?
- ¿Qué tecnologías intervienen en este proceso?
- ¿Qué técnicas de minería de datos aprenderás a utilizar?
- ¿Dónde podemos aplicar la minería de datos?
- ¿Qué retos y limitaciones existen actualmente en el área?

Cuando visitamos al médico, confiamos en que haya adquirido un amplio
**conocimiento** sobre medicina, a partir de interactuar con su entorno y de su
capacidad para procesar la información que lo rodea. Utilizando este conocimiento,
es capaz de darnos un diagnóstico acertado.

De manera análoga, si visitáramos a un *robot-médico*, este también debería contar
con un amplio conocimiento sobre medicina, aunque en su caso se trataría de una
inteligencia artificial.

Según Russell y Norvig, el término **inteligencia artificial** se aplica cuando una
máquina imita funciones cognitivas que los humanos asociamos con otras mentes,
como por ejemplo **aprender** y **resolver problemas**. Una de las primeras
aplicaciones de la inteligencia artificial fueron los **sistemas expertos**, los
cuales pueden emular la toma de decisiones de un humano experto, como un médico.

Un exponente importante de este tipo de sistemas fue *MYCIN*, el cual se basaba en
un motor de inferencia sencillo que manejaba una base de conocimiento de
aproximadamente 500 reglas. Estas reglas representaban el conocimiento de los
médicos.

Una desventaja importante de este enfoque es que se requiere un gran esfuerzo
para **extraer manualmente el conocimiento** de los expertos. Este proceso
implica entrevistarlos mediante técnicas de *elicitación del conocimiento*,
representar dicho conocimiento de alguna forma y almacenarlo en una base de
conocimiento.

Cualquier desarrollador que haya tenido que “extraer” los requerimientos de un
usuario te dirá que esta es una tarea cercana a lo imposible.

Esto nos lleva a una pregunta natural:  
¿es posible extraer conocimiento humano **a partir de otras fuentes**?  
Por ejemplo, si contamos con una base de datos con miles de diagnósticos médicos
realizados previamente, ¿podemos extraer nuevo conocimiento a partir de ella?

Este tipo de preguntas son precisamente las que intenta responder el área de
investigación en inteligencia artificial y, como veremos más adelante, en esta
búsqueda intervienen muchas otras áreas del conocimiento.

Descubrimiento de conocimiento en bases de datos
------------------------------------------------

Al proceso de extraer conocimiento útil a partir de los datos se le denomina
**Descubrimiento de Conocimiento en Bases de Datos**, o **KDD** (*Knowledge
Discovery from Databases*).

Este proceso puede realizarse de forma manual, cuando expertos en algún dominio
analizan bases de datos para descubrir patrones que les ayuden a tomar decisiones.
Un ejemplo ilustrativo aparece en la película *The Big Short*, donde el
inversionista Michael Burry analiza grandes volúmenes de datos para predecir el
colapso de la burbuja inmobiliaria.

Como podemos imaginar, este proceso no es trivial. No se trata simplemente de
hacer consultas en una base de datos, sino de descubrir **patrones complejos** que
involucran múltiples variables y relaciones no lineales.

En general, la extracción de conocimiento se realiza de manera **semi-automática**,
aplicando técnicas como:

- aprendizaje automático,
- reconocimiento de patrones,
- estadística,
- procesamiento de lenguaje natural,
- visualización de datos,
- ingeniería de software.

La **minería de datos** es solo uno de los componentes del proceso de KDD.

El proceso de KDD
-----------------

El proceso de KDD, según el esquema de Brachman y Anand, se muestra en la
Figura siguiente.

.. figure:: ../img/Proceso-KDD.png
   :width: 80%
   :align: center
   :alt: Proceso de KDD según Brachman y Anand

   Proceso de Descubrimiento de Conocimiento en Bases de Datos (KDD).

1. **Definición del objetivo.**  
   Se identifica el objetivo del proceso de KDD. Por ejemplo, una empresa de
   telefonía móvil puede estar interesada en identificar a los clientes que
   probablemente cancelarán su contrato (*churn*).

2. **Selección y recolección de datos.**  
   Se seleccionan los datos necesarios, los cuales pueden estar distribuidos en
   diferentes bases de datos o provenir de sensores y sistemas externos.

3. **Preprocesamiento.**  
   Los datos se limpian de errores, valores faltantes, inconsistencias y problemas
   de formato. Este paso suele ser uno de los más costosos.

4. **Transformación.**  
   Los datos se transforman para facilitar su análisis. Por ejemplo, un documento
   de texto puede convertirse en un vector numérico.

5. **Selección de la tarea de minería de datos.**  
   Dependiendo del objetivo, se selecciona la tarea adecuada: clasificación,
   regresión, agrupamiento, etc.

6. **Análisis exploratorio y selección de algoritmos.**  
   Se experimenta con distintos algoritmos y se ajustan sus parámetros.

7. **Minería de datos.**  
   Se aplican los algoritmos seleccionados para obtener los modelos.

Los resultados de este paso son **modelos** que describen los datos. Por ejemplo,
un conjunto de reglas que permitan predecir si un cliente cancelará su
suscripción.

Un ejemplo de regla podría ser:

.. code-block:: text

   SI el cliente tiene un promedio mayor a 7 días de retraso
     AND su promedio de llamadas del mes es menor que 10
   ENTONCES:
     el cliente cancelará el servicio

Los modelos no siempre son fácilmente interpretables por humanos. Por ejemplo,
el resultado de un algoritmo de agrupamiento puede ser simplemente un conjunto
de grupos que deben analizarse posteriormente.

Una propiedad fundamental de los modelos es su capacidad de **generalización**,
es decir, su habilidad para hacer predicciones sobre datos que no han sido vistos
durante el entrenamiento.

8. **Interpretación y evaluación.**  
   Se evalúa la utilidad del conocimiento descubierto y se visualizan los modelos.

9. **Uso del conocimiento.**  
   Finalmente, el conocimiento obtenido se utiliza para la toma de decisiones,
   integración en sistemas o generación de reportes.

Algunas definiciones y tecnologías complementarias
--------------------------------------------------

Minería de datos
~~~~~~~~~~~~~~~~

La minería de datos es el componente central en el que se enfoca este libro. Se
puede definir como:

**El proceso de búsqueda de patrones y modelos mediante la aplicación de
algoritmos a grandes cantidades de datos.**

Aprendizaje automático (*Machine Learning*)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

El aprendizaje automático es el área encargada de desarrollar algoritmos capaces
de aprender a partir de los datos y hacer predicciones. En este libro utilizaremos
frecuentemente el término *Machine Learning*.

Ciencia de datos
~~~~~~~~~~~~~~~~

La ciencia de datos busca extraer conocimiento científico a partir de los datos.
Es un campo interdisciplinario que combina estadística, matemáticas y ciencias
computacionales para modelar sistemas complejos.

Computación inteligente
~~~~~~~~~~~~~~~~~~~~~~~

La computación inteligente se refiere a métodos inspirados en la naturaleza, como
las redes neuronales artificiales, la lógica difusa y la computación evolutiva,
para resolver problemas complejos del mundo real.

Big Data
~~~~~~~~

El término *Big Data* se refiere a escenarios donde el volumen, la velocidad o la
variedad de los datos hacen que los métodos tradicionales sean inadecuados.

Cloud Computing
~~~~~~~~~~~~~~~

El *Cloud Computing* permite acceder bajo demanda a recursos computacionales,
almacenamiento y servicios de *Machine Learning*, facilitando la implementación
de sistemas de KDD a gran escala.

Aplicaciones
------------

La minería de datos se utiliza principalmente para modelar sistemas complejos y
no lineales, procesar ruido, analizar datos del Internet de las Cosas, reconocer
patrones, apoyar decisiones de negocio y realizar minería de textos.

Las tareas de la minería de datos
---------------------------------

Clasificación
~~~~~~~~~~~~~

La clasificación consiste en asignar un objeto a una clase o categoría conocida,
basándose en ejemplos previamente etiquetados.

Regresión
~~~~~~~~~

La regresión es similar a la clasificación, pero la variable objetivo es un valor
continuo.

Agrupamiento
~~~~~~~~~~~~

El agrupamiento (*clustering*) busca encontrar grupos de objetos similares sin
utilizar etiquetas previas.

Análisis de asociación
~~~~~~~~~~~~~~~~~~~~~~

El análisis de asociación descubre relaciones frecuentes entre conjuntos de
elementos, comúnmente expresadas como reglas de asociación.

Detección de anomalías
~~~~~~~~~~~~~~~~~~~~~~

Esta tarea busca identificar objetos que se comportan de forma diferente al
resto de los datos.

Optimización
~~~~~~~~~~~~

La optimización consiste en encontrar soluciones óptimas y es un componente clave
en muchas técnicas de minería de datos.

Los retos que motivan a la minería de datos
-------------------------------------------

Entre los principales retos se encuentran:

- **Escalabilidad**
- **La maldición de la dimensión**
- **Datos heterogéneos y complejos**

.. figure:: img/dim.png
   :width: 70%
   :align: center
   :alt: Maldición de la dimensión

   El volumen del espacio crece exponencialmente con la dimensión.

Lectura adicional
-----------------

- Visión general de KDD  
- KD Nuggets

