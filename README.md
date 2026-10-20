# README - PRACTICA 3 - VISIÓN POR COMPUTADOR
### Jaime Cuenca Relinque y Daniel Hernández Sánchez
#### **Objetivo del informe**

En este documento se presenta la memoria de la tercera práctica de Visión por Computador

### 2.1 - Tarea 1

#### ¿Qué teníamos que conseguir?


#### ¿Cómo lo hemos hecho?


#### ¿Qué resultados hemos obtenido?


#### Imágenes adjuntando los resultados obtenidos


### 2.2 - Tarea 2

#### ¿Qué teníamos que conseguir?

En este caso, se pide desarrollar un procedimiento que extraiga las partículas presentes en la imagen "MPs_test.jpg" y clasifique las mismas en tres tipos:
-Alquitrán(TAR)
-Pellets(PEL)
-Fragmentos(FRA)

Para el desarrollo de la misma se nos facilita tres imágenes de entrenamiento cada una ella con presencia de solo un tipo de partícula. 

#### ¿Cómo lo hemos hecho?

Para el filtrado de imágenes, umbralizado, etc... se ha utilizado la función procesar_imagen() la cual se va a ocupar de realizar toda la lógica necesaria para obtener los contornos de la imagen.
Imagen original
↓
Suavizado Gaussiano (Reducir ruido | Eliminar pequeñas variaciones locales | Suavizar bordes muy irregulares)
↓
Umbralización adaptativa 
Esto resulta especialmente útil en imágenes microscópicas porque la iluminación puede variar entre distintas zonas.
Parametros:
11
Cada píxel utiliza una región local de:
Plain Text
11 × 11 píxeles
2
Se resta una pequeña cantidad al valor calculado para aumentar el contraste entre partículas y fondo.
aunque todavía con los colores invertidos respecto a lo que necesitamos.
↓
Binarización final
Primero se invierte la imagen:
255 - umbral
para conseguir que las partículas aparezcan en blanco y el fondo en negro.
Posteriormente se aplica una binarización adicional para obtener una máscara más limpia.

↓
Extracción de contornos

#### Datos IMportantes usados

cv2.RETR_EXTERNAL
Permite recuperar únicamente los contornos externos de cada partícula.
Esto evita detectar huecos o irregularidades internas como objetos independientes.

cv2.CHAIN_APPROX_SIMPLE
Reduce el número de puntos almacenados por cada contorno manteniendo su forma, disminuyendo así el consumo de memoria y el coste computacional.

#### se acaban los datos

Por un lado se ha desarrollado la función obtener_caracteristicas():
La cual a partir de los contornos encontrados en la imagen con la función cv2.findContours(), calcula las siguientes características geométricas:
area,
perimetro,
compacidad,
aspect_ratio,
solidity,
extent,
relacion_elipse,
relacion_centroide

A partir de los datos obtenidos en las imágenes de entrenamiento, se entrena un clasificador que "aprende" los posibles patrones de las características y nos permite hacer una predicción de cuantas partículas de cada tipo hay.

#### ¿Qué resultados hemos obtenido?



#### Imágenes adjuntando los resultados obtenidos




