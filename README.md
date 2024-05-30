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

## Modelo de Wealth Distribution en NetLogo web

<img width="530" alt="WealthDistribution" src="https://github.com/Victo0rcg/Programacion-III/assets/133158811/9c3a0851-5eca-4620-a10f-42932081fbb4">


## Curva de Lorenz y Coeficiente de Gini
![LorezCurve](https://github.com/Victo0rcg/Programacion-III/assets/133158811/6521cca3-38d9-420f-94b7-5cac8092197c)

## Codigo original
globals
[
  max-grain    ; maximum amount any patch can hold
  gini-index-reserve
  lorenz-points
]

patches-own
[
  grain-here      ; the current amount of grain on this patch
  max-grain-here  ; the maximum amount of grain this patch can hold
]

turtles-own
[
  age              ; how old a turtle is
  wealth           ; the amount of grain a turtle has
  life-expectancy  ; maximum age that a turtle can reach
  metabolism       ; how much grain a turtle eats each time
  vision           ; how many patches ahead a turtle can see
]

;;;
;;; SETUP AND HELPERS
;;;

to setup
  clear-all
  ;; set global variables to appropriate values
  set max-grain 50
  ;; call other procedures to set up various parts of the world
  setup-patches
  setup-turtles
  update-lorenz-and-gini
  reset-ticks
end

;; set up the initial amounts of grain each patch has
to setup-patches
  ;; give some patches the highest amount of grain possible --
  ;; these patches are the "best land"
  ask patches
    [ set max-grain-here 0
      if (random-float 100.0) <= percent-best-land
        [ set max-grain-here max-grain
          set grain-here max-grain-here ] ]
  ;; spread that grain around the window a little and put a little back
  ;; into the patches that are the "best land" found above
  repeat 5
    [ ask patches with [max-grain-here != 0]
        [ set grain-here max-grain-here ]
      diffuse grain-here 0.25 ]
  repeat 10
    [ diffuse grain-here 0.25 ]          ;; spread the grain around some more
  ask patches
    [ set grain-here floor grain-here    ;; round grain levels to whole numbers
      set max-grain-here grain-here      ;; initial grain level is also maximum
      recolor-patch ]
end

to recolor-patch  ;; patch procedure -- use color to indicate grain level
  set pcolor scale-color yellow grain-here 0 max-grain
end

;; set up the initial values for the turtle variables
to setup-turtles
  set-default-shape turtles "person"
  create-turtles num-people
    [ move-to one-of patches  ;; put turtles on patch centers
      set size 1.5  ;; easier to see
      set-initial-turtle-vars ]
  recolor-turtles
end

to set-initial-turtle-vars
  face one-of neighbors4
  set life-expectancy life-expectancy-min +
                        random (life-expectancy-max - life-expectancy-min + 1)
  set metabolism 1 + random metabolism-max
  set wealth metabolism + random 50
  set vision 1 + random max-vision
  set age random life-expectancy
end

;; Set the class of the turtles -- if a turtle has less than a third
;; the wealth of the richest turtle, color it red.  If between one
;; and two thirds, color it green.  If over two thirds, color it blue.
to recolor-turtles
  let max-wealth max [wealth] of turtles
  ask turtles
    [ ifelse (wealth <= max-wealth / 3)
        [ set color red ]
        [ ifelse (wealth <= (max-wealth * 2 / 3))
            [ set color green ]
            [ set color blue ] ] ]
end

;;;
;;; GO AND HELPERS
;;;

to go
  ask turtles
    [ turn-towards-grain ]  ;; choose direction holding most grain within the turtle's vision
  harvest
  ask turtles
    [ move-eat-age-die ]
  recolor-turtles

  ;; grow grain every grain-growth-interval clock ticks
  if ticks mod grain-growth-interval = 0
    [ ask patches [ grow-grain ] ]

  update-lorenz-and-gini
  tick
end

;; determine the direction which is most profitable for each turtle in
;; the surrounding patches within the turtles' vision
to turn-towards-grain  ;; turtle procedure
  set heading 0
  let best-direction 0
  let best-amount grain-ahead
  set heading 90
  if (grain-ahead > best-amount)
    [ set best-direction 90
      set best-amount grain-ahead ]
  set heading 180
  if (grain-ahead > best-amount)
    [ set best-direction 180
      set best-amount grain-ahead ]
  set heading 270
  if (grain-ahead > best-amount)
    [ set best-direction 270
      set best-amount grain-ahead ]
  set heading best-direction
end

to-report grain-ahead  ;; turtle procedure
  let total 0
  let how-far 1
  repeat vision
    [ set total total + [grain-here] of patch-ahead how-far
      set how-far how-far + 1 ]
  report total
end

to grow-grain  ;; patch procedure
  ;; if a patch does not have it's maximum amount of grain, add
  ;; num-grain-grown to its grain amount
  if (grain-here < max-grain-here)
    [ set grain-here grain-here + num-grain-grown
      ;; if the new amount of grain on a patch is over its maximum
      ;; capacity, set it to its maximum
      if (grain-here > max-grain-here)
        [ set grain-here max-grain-here ]
      recolor-patch ]
end

;; each turtle harvests the grain on its patch.  if there are multiple
;; turtles on a patch, divide the grain evenly among the turtles
to harvest
  ; have turtles harvest before any turtle sets the patch to 0
  ask turtles
    [ set wealth floor (wealth + (grain-here / (count turtles-here))) ]
  ;; now that the grain has been harvested, have the turtles make the
  ;; patches which they are on have no grain
  ask turtles
    [ set grain-here 0
      recolor-patch ]
end

to move-eat-age-die  ;; turtle procedure
  fd 1
  ;; consume some grain according to metabolism
  set wealth (wealth - metabolism)
  ;; grow older
  set age (age + 1)
  ;; check for death conditions: if you have no grain or
  ;; you're older than the life expectancy or if some random factor
  ;; holds, then you "die" and are "reborn" (in fact, your variables
  ;; are just reset to new random values)
  if (wealth < 0) or (age >= life-expectancy)
    [ set-initial-turtle-vars ]
end

;; this procedure recomputes the value of gini-index-reserve
;; and the points in lorenz-points for the Lorenz and Gini-Index plots
to update-lorenz-and-gini
  let sorted-wealths sort [wealth] of turtles
  let total-wealth sum sorted-wealths
  let wealth-sum-so-far 0
  let index 0
  set gini-index-reserve 0
  set lorenz-points []

  ;; now actually plot the Lorenz curve -- along the way, we also
  ;; calculate the Gini index.
  ;; (see the Info tab for a description of the curve and measure)
  repeat num-people [
    set wealth-sum-so-far (wealth-sum-so-far + item index sorted-wealths)
    set lorenz-points lput ((wealth-sum-so-far / total-wealth) * 100) lorenz-points
    set index (index + 1)
    set gini-index-reserve
      gini-index-reserve +
      (index / num-people) -
      (wealth-sum-so-far / total-wealth)
  ]
end


; Copyright 1998 Uri Wilensky.
; See Info tab for full copyright and license.
