# README - PRACTICA 3 - VISIÓN POR COMPUTADOR
### Jaime Cuenca Relinque y Daniel Hernández Sánchez
#### **Objetivo del informe**

En este documento presentamos el resumen de la tercera práctica de la asignatura, orientada a la segmentación de objetos y la medida de elementos en tiempo real. Partiendo de los métodos explicados en las prácticas previas, el propósito de este trabajo es aplicar estas herramientas para detectar, clasificar, contar monedas y tipos de residuos.

### 2.1 - Tarea 1

#### ¿Qué teníamos que conseguir?
En esta tarea utilizamos un entorno en vivo, donde el programa debía analizar cada fotograma capturado por la cámara al instante.

La tarea consistía en:

Capturar y preparar la imagen: Recibir el vídeo de la cámara en directo, pasar la imagen a escala de grises y aplicarle un filtro de desfoque (GaussianBlur) para suavizar la imagen y evitar errores por ruido o suciedad.

Separar las monedas del fondo: Aplicar la técnica de binarización de Otsu (cuyo código hemos visto en esta práctica) para separar claramente los objetos del fondo, e ignorar aquellas figuras que no fuesen redondas mediante un filtro de forma (compacidad).

Calibrar el tamaño con un clic: Conseguir que el usuario pudiese indicarle al programa la escala real haciendo clic sobre una moneda de referencia (en nuestro caso, la de 1 Euro, que mide 23.25mm de diámetro). Con ese simple gesto, el programa calcula cuántos píxeles equivalen a un milímetro.

Clasificar y sumar el dinero: Una vez obtenida la medida en milímetros de cada moneda detectada, comparar ese tamaño con una lista de diámetros oficiales de monedas de Euro, identificar de cuál se trata y mostrar el importe total sumado en la propia pantalla.

#### ¿Cómo lo hemos hecho?

Para resolver esta práctica nos basamos en los algoritmos y códigos revisados en la sesión. El procedimiento seguido ha sido el siguiente:

Configuración de la cámara y la interfaz:

Intentar realizar la práctica enfocando las monedas con la cámara integrada del portátil resultaba muy incómodo y difícil de encuadrar. Por ello, optamos por instalar una aplicación móvil que nos permitiese usar la cámara del teléfono como cámara web (conectándola directamente al programa).

También asociamos una función al ratón (cv2.setMouseCallback) para que la ventana detectase cuándo y dónde hacíamos clic

Procesamiento de cada fotograma:

Pasamos el fotograma a grises y lo suavizamos con un filtro gaussiano de (5, 5). Aplicamos el umbralizado automático de Otsu (cv2.THRESH_OTSU) para aislar los objetos.Para detectar los contornos empleamos la función cv2.findContours con la opción cv2.RETR_EXTERNAL (tal y como vimos en las explicaciones de clase), la cual extrae únicamente los contornos exteriores descartando detalles internos innecesarios.

Filtrado y calibración:

Eliminamos pequeñas manchas o ruido en la imagen descartando zonas muy pequeñas, probamos primero con 300 pero cogía formas que no eran monedas así que subimos a 500. Para asegurarnos de detectar solo monedas, comprobamos la relación entre el perímetro y el área (compacidad); si la forma no era lo suficientemente circular, el programa la ignoraba.

Al principio lo hicimos solo con círculos pero nos dimos cuenta de que si la cámara no estaba perfectamente en paralelo con la mesa no pillaba bien los pixeles de las monedas. Es por ello que decidimos ajustar una elipse a cada contorno válido para medir su tamaño en píxeles.

Al hacer clic sobre la moneda de 1 Euro, el programa calcula la distancia desde el clic hasta el centro de la moneda más cercana. Si la toca, fija el factor de conversión (píxeles por milímetro) y borra el registro del clic.

Identificación y muestra de resultados:

Con la medida en milímetros de cada moneda, el programa busca cuál es el diámetro oficial que más se le parece dentro de nuestra lista y le asigna su valor correspondiente.

Finalmente, el programa dibuja los bordes de las monedas en verde y muestra en la parte superior el dinero total acumulado. Si todavía no se ha calibrado, muestra un texto en rojo indicando que debemos hacer clic sobre la moneda de 1 Euro.

#### ¿Qué resultados hemos obtenido?

Tras poner a prueba el programa con la cámara del móvil en tiempo real, hemos llegado a las siguientes conclusiones:

Facilidad de uso con la app móvil: El uso de la cámara del teléfono facilitó enormemente el encuadre vertical sobre la mesa en comparación con la cámara del portátil, logrando una captura mucho más clara y estable de las monedas.

Precisión en la clasificación: El sistema cuenta y clasifica correctamente las monedas en estático. Gracias al umbralizado de Otsu y a la función cv2.findContours con RETR_EXTERNAL, la detección de los bordes exteriores funciona de forma muy fiable.

Necesidad de recalibración al mover la cámara: Comprobamos que el recuento es exacto siempre que la cámara permanezca en una posición fija. Sin embargo, al mover o cambiar la altura del teléfono respecto a la mesa, la relación entre píxeles y milímetros varía de inmediato, por lo que se hace necesario volver a hacer clic sobre la moneda de 1 Euro para recalibrar la medida.

Como se puede observar al ser los diámetros de algunas monedas entre ellas muy parecidas como 50cent-1euro o 10cent-5cent a veces se equivoca pues es muy difícil distinguir las dado que la cantidad de píxeles es muy parecida.

#### Imágenes adjuntando los resultados obtenidos

antes de clicar sobre la moneda de 1 euro 

<img width="792" height="627" alt="image" src="https://github.com/user-attachments/assets/1483fdab-606c-4e39-a075-bd5db0b59093" />

Tras clicar sobre la moneda de 1 euro ya liga cada moneda a un valor de la lista

<img width="793" height="654" alt="WhatsApp Image 2026-10-21 at 20 33 32" src="https://github.com/user-attachments/assets/379b3efa-f7fc-4d08-a3b3-186f9029a964" />

#### Código Adicional

Como propuesta inicial para abordar el recuento en escenarios con monedas solapadas, optamos por implementar la Transformada de Hough para círculos (cv2.HoughCircles()). Aunque este algoritmo lograba detectar con cierto éxito los bordes y contornos individuales de las monedas superpuestas en las pruebas realizadas sobre imágenes estáticas, presentó limitaciones importantes a la hora de determinar con precisión el diámetro real y clasificar correctamente el valor monetario de cada una.

Debido a que los resultados no alcanzaron la precisión y robustez requeridas para el cálculo del importe total, decidimos mantener cv2.findContours() como el método principal y definitivo para el desarrollo de ambas tareas de la práctica. No obstante, hemos conservado la aproximación basada en cv2.HoughCircles() a modo de tarea adicional y exploratoria, demostrando las posibilidades y limitaciones del conteo de elementos con solapamiento dentro del flujo de visión por computador.

<img width="642" height="507" alt="image" src="https://github.com/user-attachments/assets/adecbb6b-e205-4451-ad65-18c534e57238" />

como podemos observar está contando una moneda de más, pero el resultado para monedas solapadas es bastante bueno.

<img width="796" height="177" alt="image" src="https://github.com/user-attachments/assets/508c3552-9751-494e-ba16-058a0cd3380c" />

Como podemos observar no relaciona bien el valor de las monedas con la propia moneda. Es por ello que hemos decidido usar cv2.findContours()

### 2.2 - Tarea 2

#### ¿Qué teníamos que conseguir?

En este caso, se pide desarrollar un procedimiento que extraiga las partículas presentes en la imagen "MPs_test.jpg" y clasifique las mismas en tres tipos:

-Alquitrán(TAR)

-Pellets(PEL)

-Fragmentos(FRA)

Para el desarrollo de la misma se nos facilita tres imágenes de entrenamiento cada una ella con presencia de solo un tipo de partícula. 

#### ¿Cómo lo hemos hecho?

Para comenzar se explican los métodos usados y creados para la clasificación de las imagenes:

En primer lugar, se ha creado la función procesar_imagen() la cual se va a ocupar de realizar toda la lógica necesaria para sacar contornos en la imagen. En este caso se ha optado por esta versión pero comentado se ofrece otro procesado de imagen distinto.

***Parámetros de entrada***: 

**ruta_imagen**: Será la ruta a la imagen que queramos procesar.

***Funcionamiento***:

Se comienza leyendo la imagen (en escala de grises) y realizando un suavizado Gaussiano con el objetivo de reducir ruido y eliminar pequeñas variaciones locales. Posteriormente usamos una umbralización adaptativa para separar automáticamente las partículas del fondo, al estar invertidos los blancos y negros de la imagen tras el umbralizado se deben volver a invertir (255-umbral) y se hace un binarizado final para obtener una máscara mas limpia y homogenea. Con la imagen ya preparada para sacar contornos de usa la función cv2.findContours() y se sacan los contornos externos (RETR_EXTERNAL) de la imagen.

***Parámetros de salida***:

**imgorig**: imagen original en escala de grises.

**imth**: imagen binaria tras la segmentación.

**contornos**: contornos detectados de las partículas.


En segundo lugar, se ha creado obtener_etiqueta_real() la cual nos da la etiqueta asociada a un elemento del CSV con las notaciones correctas.

***Parámetros de entrada***:

**cx**: Posición del centroide en el eje X de una particula.

**cy**: Posición del centroide en el eje X de una particula.

**ground_truth**: Lista con la información del CSV

***Funcionamiento***: 

A partir de la posición del centroide pasado por parámetro, realiza una comparativa para saber si hay alguna particula en esa posición y obtiene su etiqueta. 

***Parámetros de salida***:

**label_real**: Si encuentra una coincidencia devuelve el label asociado. (None en caso de existir la particula)

Por ultimo en las funciones de procesado, se ha creado obtener_caracteristicas():
La cual a partir de los contornos encontrados en la imagen con la función cv2.findContours(), calcula características geométricas para el clasificado.


***Parámetros de entrada***: 

**contornos**: Lista de contornos obtenidos mediante cv2.findContours().

***Funcionamiento***: 

La función recorre todos los contornos detectados y calcula una serie de características geométricas para cada partícula. Estas características son todas devueltas para poder clasificar las partículas según los datos obtenidos. 

***Parámetros de salida***:

**area**: Área del contorno en píxeles.

**perimetro**: Longitud del borde de la partícula.

**compacidad**: Mide la relación entre el perímetro y el área de la partícula.

**aspect_ratio**: Relación entre anchura y altura del rectángulo contenedor.

**solidity**: Relación entre el área real de la partícula y el área de su envolvente convexa.

**extent**: Porcentaje del rectángulo contenedor ocupado por la partícula

**relacion_elipse**: Relación entre el eje mayor y el eje menor de la elipse ajustada.

**relacion_centroide**: Relación entre la distancia mínima y máxima desde el centroide hasta el contorno.

**cx**: Coordenada X del centroide de la partícula.

**cy**: Coordenada Y del centroide de la partícula.

Una vez definidas las funciones de procesado y extracción de características, ya se puede comenzar a explicar el flujo de trabajo.

##### Preparación del conjunto de evaluación

Se comienza cargando la imagen de prueba MPs_test.jpg junto con el fichero de anotaciones MPs_test_bbs.csv, que contiene la posición y tipología de las partículas etiquetadas manualmente. Las anotaciones se almacenan en una estructura de datos llamada ground_truth que se usará posteriormente para la matriz de confusión.
Para el análisis de las partículas la imagen MPs_test.jpg es transformada con la función procesar_imagen() a una lista de contornos y posteriormente se obtienen sus características con obtener_caracteristicas(). 

##### Obtención de las etiquetas reales

Una vez ya tenemos las características, se relacionan los contornos encontrados con los contornos anotados con la función obtener_etiqueta_real() y se guardan para comparar con la predicción.

##### Construcción del conjunto de entrenamiento

Una vez tenemos las caracteristicas extraidas de la imagen que se va analizar se pasa al entrenamiento del clasificador en este caso se ha decidido usar un DecisionTreeClassifier. Este clasificador 
Las imágenes de entrenamiento contienen partículas pertenecientes a una única categoría:

FRA.png → Fragmentos

PEL.png → Pellets

TAR.png → Alquitrán

Cada imagen es procesada mediante la función procesar_imagen() obteniéndose los contornos de todas las partículas presentes.

Posteriormente se calculan sus características geométricas con obtener_caracteristicas() y se construye el conjunto de entrenamiento con los vectores de características de todas las partículas y un vector con su etiqueta.

##### Entrenamiento del árbol de decisión

Para realizar la clasificación se emplea un árbol de decisión configurado con una profundidad máxima de cuatro niveles. Durante el entrenamiento, el algoritmo analiza las características geométricas de las partículas y aprende automáticamente reglas que permiten separar las distintas clases.
De esta forma se evita definir manualmente umbrales de decisión y se obtiene una clasificación basada en los datos de entrenamiento. 

#### ¿Qué resultados hemos obtenido?

Una vez entrenado el árbol de decisión con las características geométricas extraídas de las muestras de entrenamiento, se realiza una predicción con la función modelo.fit(muestras_train, label_train).
A partir de la predicción usando la librería sklearn.metrics, se puede estudiar distintas métricas, en este caso, se ha estudiado:

**Accuracy = 0.363**

El modelo obtiene una exactitud del 36.3%, lo que indica que aproximadamente una de cada tres partículas es clasificada correctamente. Aunque el valor no es elevado se debe tener en cuenta que la utilización exclusiva de características geométricas, sin recurrir a técnicas de aprendizaje profundo, hace que haya un porcentaje muy bajo de acierto.

**Precision = 0.694**

La precisión alcanza un valor de 69.4%. Esto indica que, cuando el clasificador asigna una etiqueta a una partícula, dicha predicción suele ser razonablemente fiable.

**Recall = 0.363**

El recall obtenido es de 36.3%, significativamente inferior a la precisión. Este comportamiento revela que el árbol de decisión está dejando sin detectar correctamente una gran cantidad de partículas reales, en especial aquellas clases cuyos descriptores geométricos son similares

**F1 Score  = 0.401**

El valor de 0.401 refleja el compromiso entre precisión y recall.

Por último se puede observar la matriz de confusión obtenida, con los aciertos y fallos en detalle.

<img width="699" height="510" alt="image" src="https://github.com/user-attachments/assets/77805ddc-b61a-4275-99ba-171902a8341b" />

Como se puede observar, la mayoría de errores se producen entre FRA y PEL, lo que indica que ambas categorías presentan características geométricas muy similares para el conjunto de descriptores utilizado. Con el fin de intentar que la predicción sea mas acertada se han utilizado diferentes umbrales para ver cual se adaptaba mejor a la imagen, de ahí que existan dos funciones de procesado de imagen, dependiendo la que se quiera utilizar.

De manera adicional, se pregunto a varias inteligencias artificiales si podría mejorar el porcentaje de acierto que habíamos obtenido y nos propuso las siguientes mejoras:

Relleno de siluetas en procesar_imagen: Se añade una línea para que los contornos detectados se dibujen rellenos en blanco (cv2.drawContours(imth, contornos, -1, 255, -1)). 

Paso de imgorig a obtener_caracteristicas: Se añade para calcular la intensidad media de gris por partícula (media_gris), permitiendo clasificar correctamente sin romper la forma de las listas.

Por último arreglo de erratas en los cálculos geometricos.

Una vez añadidos todos estos cambios, se volvio a realizar la predicción la matriz en este caso fue:

<img width="730" height="586" alt="image" src="https://github.com/user-attachments/assets/7a130f89-c15a-4e98-813a-189e1f732fe1" />

Como se puede observar ahora, los fragmentos son detectados en su gran mayoría pero por otro lado baja el acierto en las otras dos categorías. Por otro lado, ahora se puede observar como las métricas son bastante superiores.

<img width="568" height="77" alt="image" src="https://github.com/user-attachments/assets/6ae25d00-a7d1-455a-b995-dbec0a6a098a" />

**Cabe destacar, que el codigo desarrollado mejorado por inteligencia articial ha sido añadido de manera externa al ya desarrollado ya que se considera que aunque las métricas sean mejores estan lejos de ser perfectas**


#### Imágenes adjuntando los resultados obtenidos

Si se quiere observar las características geométricas existe la función imprimir_resultado(), usándola con los datos que se ha conseguido de entrenamiento.

TAR: 
Número de objetos: 9

Área
Media: 1431.2222222222222
Std: 837.8501626554287
Mediana: 1438.0

Perímetro
Media: 154.93560832076602
Std: 79.58271103712252
Mediana: 172.50966620445251

Compacidad
Media: 19.629876717286972
Std: 5.213597223284835
Mediana: 18.170966469036976

Aspect Ratio
Media: 0.9979405749348691
Std: 0.14197386733806824
Mediana: 0.9782608695652174

Solidity
Media: 0.9058116018099168
Std: 0.04943008575248918
Mediana: 0.9211401425178147

Extent
Media: 0.5746253274408738
Std: 0.08446827918941069
Mediana: 0.610512129380054

Elipse
Media: 1.5463420161595094
Std: 0.5506531922323535
Mediana: 1.4485541474714376

Centroide
Media: 0.4931703937607515
Std: 0.1432657620716614
Mediana: 0.49736463743969167

PEL: 
Número de objetos: 31

Área
Media: 164.06451612903226
Std: 174.8964551705308
Mediana: 140.5

Perímetro
Media: 141.0642398788083
Std: 120.15826416553769
Mediana: 102.42640614509583

Compacidad
Media: 130.7866805764562
Std: 96.74231165226932
Mediana: 111.7655913051606

Aspect Ratio
Media: 1.7023917003780578
Std: 1.318818467010442
Mediana: 1.1791044776119404

Solidity
Media: 0.448193819411499
Std: 0.2349119172677839
Mediana: 0.41336971350613916

Extent
Media: 0.23516078805825827
Std: 0.12376646025109012
Mediana: 0.23421926910299004

Elipse
Media: 2.4259039238765743
Std: 1.4545830610584933
Mediana: 1.8027496015350777

Centroide
Media: 0.17387981525091903
Std: 0.1648709085024149
Mediana: 0.11926535078992756

FRA: 
Número de objetos: 48

Área
Media: 107.84375
Std: 157.1440142383333
Mediana: 53.75

Perímetro
Media: 95.53112418204546
Std: 104.93412249612312
Mediana: 66.59797883033752

Compacidad
Media: 94.36747694392284
Std: 70.81579864047582
Mediana: 79.2095351036753

Aspect Ratio
Media: 1.6877896390136486
Std: 0.9864110262506973
Mediana: 1.4722222222222223

Solidity
Media: 0.48449151056747014
Std: 0.14191524665194177
Mediana: 0.4848842195540309

Extent
Media: 0.21587846099632677
Std: 0.09227938535977578
Mediana: 0.20574012550756737

Elipse
Media: 3.5908783190439237
Std: 2.3008176905293993
Mediana: 3.016996907754522

Centroide
Media: 0.10147086325153291
Std: 0.08497338600044925
Mediana: 0.08574171284305544

Si se quiere ver la imagen sobre la que se sacan contornos, el propio código lo muestra.

<img width="282" height="372" alt="image" src="https://github.com/user-attachments/assets/d1f3c4bd-67c6-4eb9-98bb-30d414c7cc2d" />


