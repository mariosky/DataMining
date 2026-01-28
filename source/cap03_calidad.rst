Calidad de los Datos
====================

Uno de los principios más antiguos de la informática dice: **“entra basura, sale basura”**.
Se refiere a que, si un programa recibe datos incorrectos o sin sentido, la salida será
igual de mala. Este principio también aplica a la ciencia de datos: el éxito del proceso
depende, en gran medida, de la validez de los datos que utilizamos.

Asegurar la calidad de los datos que recolectamos es uno de los mayores retos de la minería
de datos; por eso es fundamental realizar un proceso previo de verificación y **limpieza de
datos**.

Para darnos una idea de la complejidad de estas tareas, consideremos la recolección de datos
para el desarrollo de un `Observatorio de Lesiones
<http://conapra.salud.gob.mx/Interior/Observatorio_Nacional_Lesiones.html>`_. El objetivo de
estos sistemas, según la Secretaría de Salud, es *generar datos y evidencia científica para la
prevención de lesiones ocasionadas por accidentes viales*.

En un sistema así es indispensable contar con información confiable sobre los accidentes de
tráfico, pero, como veremos, esto no es nada fácil. Para empezar, atender un accidente involucra
a varias organizaciones: Cruz Roja, paramédicos, policía, hospitales, *call centers* de emergencia,
rescatistas, agencias de seguros, entre otras. También se involucran transeúntes, vehículos,
pasajeros, edificios, reporteros, etc.

Para obtener información sobre lo sucedido normalmente se entrevista a testigos e involucrados.
Desde ahí comienza el problema: cada persona puede tener su versión de los hechos, puede cambiarla,
o incluso estar incapacitada para declarar. Además, cada dependencia recolecta únicamente la
información que necesita para cumplir su propósito, y por lo tanto cada una tiene una versión
parcial de lo ocurrido. Para complicar más la situación, el accidente puede relacionarse con
eventos posteriores: por ejemplo, una persona puede sufrir secuelas mucho tiempo después.

La tarea de integrar los datos requiere cooperación entre dependencias, lo cual nos da un primer
golpe de realidad:

.. important::

   En muchas ocasiones **no estará en nuestras manos** la calidad de los datos ni tendremos
   control sobre la fuente.

Por lo anterior, la minería de datos se enfoca (en términos prácticos) en dos tareas básicas:

1. Detección y corrección de problemas en los datos.
2. Utilización de algoritmos que puedan tolerar datos de mala calidad.

Dicho de otra manera: **nuestro objetivo no es** gestionar o controlar la calidad desde la
captura, porque eso implicaría intervenir en los sistemas de recolección.

Decimos que un producto o servicio es de buena calidad si satisface necesidades. En el caso de
los datos, debemos asegurarnos de que sirvan para nuestros propósitos. Por ejemplo, si una tarea
del observatorio es optimizar la ubicación de estaciones de rescate, necesitaremos la ubicación
exacta de los accidentes. Aquellos registros que solo incluyan la zona o la calle podrían no
tener la calidad necesaria para esta tarea, aunque sí podrían ser útiles para otras.

A continuación revisaremos algunos problemas comunes que afectan la calidad de los datos.

Fuentes de datos poco confiables
--------------------------------

Muchas colecciones de datos disponibles no se recolectaron originalmente para realizar una
investigación científica, por lo que su autenticidad y calidad pueden variar mucho.

Entre las diferencias más comunes entre la recolección de datos comercial y la recolección con
fines científicos están las siguientes:

1. Las empresas no necesariamente adoptan procedimientos científicos (por ejemplo, muestreos
   aleatorios o mecanismos para evitar sesgos). Su objetivo es producir ganancias, no hacer ciencia.
   En consecuencia, el muestreo suele hacerse sobre la población objetivo del negocio y no sobre el
   público general.

2. Los métodos de recolección no son públicos y pueden cambiar sin aviso.

3. Las plataformas comerciales no pueden hacerse responsables de la autenticidad o validez del dato.
   Por ejemplo, al usar datos de una red social como Twitter debemos considerar cuentas de *bots*,
   usuarios pagados para producir contenido, anuncios y publicaciones tipo *spam*.

Como podemos ver, la calidad no solo depende de la medición o captura, sino del **procedimiento**
utilizado para recolectar.

Errores de medición y recolección de datos
------------------------------------------

Llamamos **errores de medición** a problemas que ocurren durante el proceso de medición o captura.
En atributos continuos, un error puede entenderse como la diferencia entre el valor real y el valor
medido.

También se presentan errores cuando un sensor no tiene precisión suficiente o cuando la medición se
trunca al almacenarse en una variable con escala o precisión inadecuada.

Un **error de recolección** se refiere a fallas de captura al nivel de objetos o atributos. Por
ejemplo:

- omitir el valor de un atributo,
- capturar un objeto en una categoría equivocada,
- registrar un valor en la unidad incorrecta.

Valores desconocidos
~~~~~~~~~~~~~~~~~~~~

En muchas ocasiones el valor de algunos atributos no se conoce. Por ejemplo, en un accidente pudo
no recolectarse la edad del conductor, el número de pasajeros o la información meteorológica.

Normalmente se indica la ausencia de valor con etiquetas como ``NULL``, ``None`` o ``Nil``.
Necesitamos decidir cómo tratar los casos donde el valor es desconocido. Algunas alternativas son:

- **Estimar el valor.** Puede ser peligroso: el valor real podría ni siquiera ser plausible para
  la población o podría inducir sesgos.

- **Eliminar el objeto.** Algunos algoritmos no procesan objetos con valores faltantes, o podemos
  decidir que ese registro no aporta información suficiente.

- **Utilizar un valor por defecto.** Por ejemplo, 0 o “desconocido”. Esta opción requiere cuidado,
  porque puede introducir valores artificiales con significado estadístico no deseado.

- **Agregar objetos con todos los valores posibles.** Incluso se pueden ponderar los valores de
  acuerdo con su probabilidad de ocurrencia.

Valores atípicos
----------------

En ocasiones algunos atributos toman valores muy distintos (o alejados numéricamente) del resto.
A estos valores se les llama **atípicos** (*outliers*). A veces se deben a errores de lectura o
procedimientos y conviene eliminarlos, pero también pueden aparecer “legítimamente” en cualquier
distribución por azar o por fenómenos reales raros.

Los valores atípicos pueden distorsionar descripciones estadísticas. Por ejemplo, si medimos los
caballos de fuerza de 8 autos en un estacionamiento, la mayoría podría estar entre 50 y 170 hp,
pero si aparece un *Mercedes F1 W06 Hybrid* con 950 hp, ese valor sería un *outlier* claro.

Consideremos una muestra (simplificada) de ``auto-mpg.data``:

.. list-table::
   :header-rows: 1
   :widths: 10 30

   * - hp
     - nombre
   * - 91.0
     - audi 100ls
   * - 115.0
     - saab 99le
   * - 53.0
     - honda civic
   * - 180.0
     - pontiac grand prix lj
   * - 140.0
     - dodge diplomat
   * - 125.0
     - cadillac eldorado
   * - 950.0
     - Mercedes F1 W06 Hybrid

En este caso, la mediana sería 125 y la media 236. Debido al valor atípico, la mediana refleja
mejor la potencia de un auto si lo seleccionamos al azar de un estacionamiento.

Los valores atípicos también pueden alertarnos de objetos que pertenecen a otra población: en
este ejemplo, el Mercedes corresponde al mundo de la Fórmula 1.

Una tarea común de limpieza es identificar *outliers* para removerlos; pero en algunos problemas,
encontrarlos es el objetivo principal. Por ejemplo, un *outlier* podría ser una transacción
fraudulenta o una `persona de interés <https://en.wikipedia.org/wiki/Person_of_interest>`_.

Este tema aparecerá a lo largo del material, incluso al evaluar algoritmos: un resultado “demasiado
bueno” en un experimento podría ser un caso atípico que no se repite al hacer más pruebas.

Datos con ruido
---------------

En algunas publicaciones se llama **ruido** a las imperfecciones en los datos, análogo a la
interferencia en una señal de radio. En análisis de datos, el ruido incluye errores de medición y
recolección, valores atípicos e inconsistencias.

En ocasiones es necesario perturbar o agregar ruido a los datos:

- para conservar la privacidad de la fuente,
- o para probar qué tan robusto es un algoritmo.

Para agregar ruido a un conjunto de datos normalmente se añaden componentes aleatorios. Por ejemplo,
se pueden agregar nuevos objetos con atributos aleatorios.

De la calidad al preprocesamiento
---------------------------------

En la sección anterior vimos que los datos del mundo real rara vez llegan “limpios”: pueden tener
valores faltantes, errores de captura, ruido, sesgos de recolección y valores atípicos. En ese
contexto, **preprocesar** no es un lujo, sino una condición práctica para poder modelar.

Es útil pensar la calidad de los datos y el preprocesamiento como dos etapas conectadas:

- **Calidad de los datos** se enfoca en *identificar problemas* (qué está mal, qué falta, qué no es
  confiable, qué no representa a la población, etc.).
- **Preprocesamiento** se enfoca en *preparar una representación computable* (y, cuando se puede,
  mitigar problemas) para que los algoritmos puedan operar de forma estable y eficiente.

En otras palabras: la limpieza de datos es una parte del preprocesamiento, pero el
preprocesamiento va más allá. Incluso si no hubiera errores de captura, seguiríamos transformando
los datos para:

- comparar variables que viven en escalas muy distintas,
- reducir dimensionalidad,
- codificar categorías,
- construir características más informativas,
- disminuir costo computacional sin perder señal.

También es importante reconocer un límite: **no todo se “arregla” con preprocesamiento**. Si un
conjunto de datos tiene un sesgo de origen (por ejemplo, fue recolectado solo en cierta población),
podemos limpiar valores y escalar variables, pero el sesgo seguirá ahí. Por eso, antes de aplicar
transformaciones conviene preguntarnos:

- ¿Qué representa realmente este conjunto de datos?
- ¿Qué variables son confiables y cuáles son aproximaciones?
- ¿Qué errores afectan el resultado (y cuáles son tolerables)?
- ¿Qué supuestos exige el algoritmo que vamos a usar?

Con esta idea en mente, pasemos a la etapa de **preprocesamiento**, donde tomaremos objetos crudos
y los convertiremos en representaciones favorables para los algoritmos de minería de datos y
aprendizaje automático.
