# Modelo de Distribucion de Riqueza
## Antecendentes
En 1969, el economista de la Universidad de Maryland Thomas Schelling, realizo una publicacion llamda "Models of Segregation", la cual no pudo continuar siendo desarrollada por las limitaciones computacionales de la epoca. 
Posteriormente, una simulacion basada en agentes desarrollada por el matematico John Conway fue aplicada a la idea de Schelling por Joshua M. Epstein y Robert Axtell, en su libro llamado Growing Artificial Societies, el cual fue distribuido junto con un CD-ROM para mostrar los avances alcanzados en el campo de la simulacion basada en agentes. El concepto del modelo construido por ellos se ha conocido como el "Sugarscape model", y desde eso, se han denominado como "sugarscape" a todos los modelos que trabajan bajo los parametros y reglas establecidas en el modelo de Epstein y Axtell.
Todos los modelos sugarscape poseen agentes, los cuales representan a los habitantes de un medio, que en el caso del modelo esta definido por una cuadricula bidimensional, y las reglas que dirigen la interaccion entre los habitantes y el medio.
El primer modelo presentado por Epstein y Axtell, considerado como el primero basado en agentes a gran escala, esta basado en una cuadricula de 51x51, en la que cada celda contiene una cantidad determinada de azucar, en cada momento o "tick", las personas se mueven a traves de la cuadricula, buscando la celda con azucar mas cercana. En el modelo las personas pueden morir, reproducirse, comercializar, intercambiar, prestar, enfermarse, sanar, contaminar, compartir informacion, contaminar y heredar recursos; dependiendo del escenario y las variables definidas en el "set-up" del modelo.
El azucar, y el modelo en general, puede ser visto como una representacion de una sociedad, y las interacciones que se dan en esta, permitiendole al examinador analizar determinadas dinamicas sociales y sus efectos.
## Wealth Distribution Model
Wealth Distribution Model o modelo de distribucion de riqueza, es un modelo de tipo sugarscape construido en NetLogo, en el cual se usan semillas en lugar de azucar, y cada camino tiene una cantidad y una capacidad de semillas, es decir, cuanto hay en el camino y cuanto podria haber. Las personas se mueven a traves del espacio de la cuadricula buscando recolectar y consumir la mayor cantidad de semillas, las cuales definen en parte su supervivencia y la cantidad de riqueza que poseen.
Inicialmente el modelo genera caminos aleatorios de semillas, y las personas buscan los caminos con mayor densidad, cada tick las personas consumen una cantidad de semillas, esta cantidad es llamada dentro del modelo como el metabolismo. La vida de cada persona esta limitada por dos factores; la cantidad de semillas, lo que significa que si una persona deja de consumirlas, fallece; y la expectativa de vida, que establece un rango de tiempo en el que la persona vive. Al morir, se produce una descendencia, que al momento de nacer posee un metabolismo aleatorio y una cantidad de semillas aleatoria, es decir, no existe una herencia mas que la ubicacion dentro de la cuadricula.
En el modelo se hace uso de una representacion grafica de la distribucion de recursos llamada la Curva de Lorenz. Esta es usada en diferentes escalas para observar el nivel de desigualdad que hay en un grupo determinado. En esta grafica se distribuye el procentaje de personas (eje x) entre su porcentaje correspondiente de recursos (eje y), y se establece una linea guia, llamada linea de la perfecta igualdad, desde el origen a 45° la cual ejemplifica lo que seria una perfecta distribucion de los recursos entre las personas. A partir de esta grafica, se puede obtener una medida numerica llamada Coeficiente de Gini, que a partir de una operacion entre las areas producidas por la linea de la perfecta igualdad y la curva de lorenz se calcula un valor numerico entre 0 y 1, siendo 0 la perfecta igualdad y 1 la desigualdad total.

## Curva de Lorenz y Coeficiente de Gini
![LorezCurve](https://github.com/Victo0rcg/Programacion-III/assets/133158811/6521cca3-38d9-420f-94b7-5cac8092197c)


## Modelo de Wealth Distribution en NetLogo web

<img width="530" alt="WealthDistribution" src="https://github.com/Victo0rcg/Programacion-III/assets/133158811/9c3a0851-5eca-4620-a10f-42932081fbb4">


# Desarrollo
## Parametros y funcionamiento inicial
Como se podra ver en la imagen, para la ejecucion del modelo se requieren varios parametros que determinan el resultado y definen el comportamiento de las personas. Como vimos anteriormente, cada camino tiene una cantidad y una capacidad de semillas, las cuales segun la cantidad que cada persona pueda recolectar y consumir determinan la riqueza del individuo y en referencia a cada individuo se hace referencia a dicha cantidad como el metabolismo, que junto con la expectativa de vida componen los parametros que se pueden definir para las personas, la expectativa de vida esta dada en forma de cantidad de ticks y la cantidad de semillas que consumen; si la cantidad de ticks excede el limite establecido o se acaba el consumo de semillas la persona muere, y da vida a una nueva con un metabolismo y cantidad de semillas dadas aleatoriamente.
Por medio de la interfaz se establecen los siguientes parametros:
  1. percent-best.land: es la densidad de los caminos que son creados con la maxima cantidad de semillas
  2. max-grain: establece la cantidad maxima de semillas que se pueden generar en un camino, mencionada anteriormente.
  3. grain-growth-interval: con que frecuencia se reemplazan las semillas consumidas
  4. num-grain-growth: cuantas semillas son creadas segun la frecuencia establecida anteriormente
  5. life-expectancy-min: el numero minimo de ticks que una persona puede vivir
  6. lif-expectancy-max: el maximo numero de ticks que una persona puede vivir
  7. metabolism-max: cantidad maxima de semillas que una persona puede recolectar por tick
  8. max-vision: distancia maxima que cada persona puede ver
El modelo permite observar un cumplimiento de la ley de Pareto, que establece que generalmente la cantidad de personas pobres es mayor a la de personas clase media y ricas, siendo mas pocas que la cantidad de personas clase media.

## Modificaciones propuestas y cambios realizados

Como guia, se hizo uso de la seccion "Expanding the model" en la informacion del modelo en la pagina de Netlogo, sobre la cual se realizaron las modificaciones realizadas al modelo.
En la ejecucion y en la informacion sobre el modelo, se aclara que al fallecer una persona, su descendencia inicia en el mismo sitio, lo que de una u otra forma genera una ventaja si es una zona densa en semillas, para cambiar esto, se hizo la creacion e implementacion de una funcion para que al morir una persona, su hijo no herede la misma ubicacion sino que se mueva a una posicion aleatoria.
De igual manera, para analizar el comportamiento del modelo, se realizo cambios en la herencia de padres a hijos, anteriormente el metabolismo, la vision y la riqueza se definian aleatoriamente, ahora con el cambio realizado los hijos heredan el 80% de la riqueza de los padres y el 50& del metabolismo. 
Se creo un parametro llamado "shortage", el cual hace referencia a escazes, que permite simular una reduccion en la cantidad de semillas en los caminos.



