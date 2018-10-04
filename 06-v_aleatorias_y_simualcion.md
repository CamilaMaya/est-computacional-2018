# Teoría básica de simulación

> "Any one who considers arithmetical methods of producing random digits is, of course, in a state of sin."
> -Von Neuman




La simulación de un modelo consiste en la construcción de un programa 
computacional que permite obtener los valores de las varibles de salida para
distintos valores de las variables de entrada con el objetivo de obtener 
conclusiones del sistema que apoyen la toma de decisiones (explicar y/o predecir
el comportamiento del sistema).  

Requerimientos prácticos de un proyecto de simulación: 

* Fuente de números aleatorios $U(0,1)$ (números pseudoaleatorios).  
* Transformar los números aleatorios en variables de entrada del modelo
(e.g. generación de muestras con cierta distribución).  
* Construir el programa computacional de simulación.  
* Analizar las distintas simulaciones del modelo para obtener conclusiones
acerca del sistema.   

## Números pseudoaleatorios

El objetivo es generar sucesiones $\{u_i\}_{i=1}^N$ de números independientes
que se puedan considerar como observaciones de una distribución uniforme
en el intervalo $(0, 1)$.

1. **Verdaderos números aleatorios. **Los números completamente aleatorios (no 
    determinísticos) son fáciles de imaginar  conceptualmente, por ejemplo 
    podemos imaginar lanzar una moneda, lanzar un dadoo una lotería.  
    En general los números aleatorios se basan en alguna fuente de aleatoreidad 
    física que puede ser teóricamente impredecible (cuántica) o prácticamente
    impredecible (caótica). Por ejemplo:
    - [random.org](http://www.random.org) genera aleatoreidad a través de ruido 
    atmosférico (el paquete `random` contiene funciones para obtener números de 
    random.org),  
    - [ERNIE](https://en.wikipedia.org/wiki/Premium_Bond#ERNIE), usa ruido 
    térmico en transistores y se utiliza en la lotería de bonos de Reino Unido.  
    - [RAND Corporation](https://www.rand.org/pubs/monograph_reports/MR1418/index2.html) 
    En 1955 publicó una tabla de un millón de números aleatorios
    que fue ampliamente utilizada. Los números en la tabla se obtuvieron de una
    ruleta electrónica.
    
    La desventaja de éstos métodos es que son costosos, tardados y no 
    reproducibles.

<div class="caja">
2. **Números pseudoaleatorios.** Los números pseudoaleatorios se generan de 
    manera secuencial con un algoritmo determinístico, formalmente se definen 
    por: 
    + **Función de inicialización**. Recibe un número (la semilla) y pone al 
    generador en su estado inicial.
    + **Función de transición**. Transforma el estado del generador.
    + **Función de salidas**. Transforma el estado para producir un número fijo 
    de bits (0 ó 1). 
    
    Una sucesión de bits pseudoaleatorios se obtiene definiendo la semilla y 
    llamando repetidamente la función de transición y la función de salidas.
</div>

  Esto implica, entre otras cosas, que una sucesión de números pseudoaletorios 
  esta completamente determinada por la semilla.
  
  <!--
  Since a computer can represent a real number with only finite accuracy, we 
  shall actually be generating integers X, between zero and some number m; the fraction
  U=X,/m will then lie between zero and one. Usually m is the word size (máximo número de 
bits que puede procesar el CPU en una llamada) of the computer -->

Buscamos que una secuencia de números pseudoaleatorios:

* no muestre ningún patrón o regularidad aparente desde un punto de vista 
estadístico, y 

* dada una semilla inicial, se puedan generar muchos valores antes de repetir el 
ciclo. 

Construir un buen algoritmo de números pseudo aleatorios es complicado, como 
veremos en los siguientes ejemplos.

#### Ejemplo: método de la parte media del cuadrado

En 1946 Jon Von Neuman sugirió usar las operaciones aritméticas de una 
computadora para generar secuencias de número pseudoaleatorios. 

Sugirió el método *middle square*, para generar secuencias de dígitos
pseudoaleatorios de 4 dígitos propuso:

1. Se inicia con una semilla de 4 dígitos. 
`seed = 9731`

2. La semilla se eleva al cuadrado, produciendo un número de 8 dígitos (si el
resultado tiene menos de 8 dígitos se añaden ceros al inicio).
`value = 94692361`

3. Los 4 números del centro serán el siguiente número en la secuencia, y se 
devuelven como resultado.
`seed = 6923`


```r
mid_square <- function(seed, n) {
   seeds <- numeric(n)
   values <- numeric(n)
   for(i in 1:n) {
       x <- seed ^ 2
       seed = case_when(
           nchar(x) > 2 ~ (x %/% 1e2) %% 1e4,
           TRUE ~ 0)
       values[i] <- x
       seeds[i] <- seed
   }
   cbind(seeds, values)
}
x <- mid_square(1931, 10) 
print(x, digits = 4)
#>       seeds   values
#>  [1,]  7287  3728761
#>  [2,]  1003 53100369
#>  [3,]    60  1006009
#>  [4,]    36     3600
#>  [5,]    12     1296
#>  [6,]     1      144
#>  [7,]     0        1
#>  [8,]     0        0
#>  [9,]     0        0
#> [10,]     0        0

x <- mid_square(9731, 100) 
print(x, digits = 4)
#>        seeds   values
#>   [1,]  6923 94692361
#>   [2,]  9279 47927929
#>   [3,]   998 86099841
#>   [4,]  9960   996004
#>   [5,]  2016 99201600
#>   [6,]   642  4064256
#>   [7,]  4121   412164
#>   [8,]  9826 16982641
#>   [9,]  5502 96550276
#>  [10,]  2720 30272004
#>  [11,]  3984  7398400
#>  [12,]  8722 15872256
#>  [13,]   732 76073284
#>  [14,]  5358   535824
#>  [15,]  7081 28708164
#>  [16,]  1405 50140561
#>  [17,]  9740  1974025
#>  [18,]  8676 94867600
#>  [19,]  2729 75272976
#>  [20,]  4474  7447441
#>  [21,]   166 20016676
#>  [22,]   275    27556
#>  [23,]   756    75625
#>  [24,]  5715   571536
#>  [25,]  6612 32661225
#>  [26,]  7185 43718544
#>  [27,]  6242 51624225
#>  [28,]  9625 38962564
#>  [29,]  6406 92640625
#>  [30,]   368 41036836
#>  [31,]  1354   135424
#>  [32,]  8333  1833316
#>  [33,]  4388 69438889
#>  [34,]  2545 19254544
#>  [35,]  4770  6477025
#>  [36,]  7529 22752900
#>  [37,]  6858 56685841
#>  [38,]   321 47032164
#>  [39,]  1030   103041
#>  [40,]   609  1060900
#>  [41,]  3708   370881
#>  [42,]  7492 13749264
#>  [43,]  1300 56130064
#>  [44,]  6900  1690000
#>  [45,]  6100 47610000
#>  [46,]  2100 37210000
#>  [47,]  4100  4410000
#>  [48,]  8100 16810000
#>  [49,]  6100 65610000
#>  [50,]  2100 37210000
#>  [51,]  4100  4410000
#>  [52,]  8100 16810000
#>  [53,]  6100 65610000
#>  [54,]  2100 37210000
#>  [55,]  4100  4410000
#>  [56,]  8100 16810000
#>  [57,]  6100 65610000
#>  [58,]  2100 37210000
#>  [59,]  4100  4410000
#>  [60,]  8100 16810000
#>  [61,]  6100 65610000
#>  [62,]  2100 37210000
#>  [63,]  4100  4410000
#>  [64,]  8100 16810000
#>  [65,]  6100 65610000
#>  [66,]  2100 37210000
#>  [67,]  4100  4410000
#>  [68,]  8100 16810000
#>  [69,]  6100 65610000
#>  [70,]  2100 37210000
#>  [71,]  4100  4410000
#>  [72,]  8100 16810000
#>  [73,]  6100 65610000
#>  [74,]  2100 37210000
#>  [75,]  4100  4410000
#>  [76,]  8100 16810000
#>  [77,]  6100 65610000
#>  [78,]  2100 37210000
#>  [79,]  4100  4410000
#>  [80,]  8100 16810000
#>  [81,]  6100 65610000
#>  [82,]  2100 37210000
#>  [83,]  4100  4410000
#>  [84,]  8100 16810000
#>  [85,]  6100 65610000
#>  [86,]  2100 37210000
#>  [87,]  4100  4410000
#>  [88,]  8100 16810000
#>  [89,]  6100 65610000
#>  [90,]  2100 37210000
#>  [91,]  4100  4410000
#>  [92,]  8100 16810000
#>  [93,]  6100 65610000
#>  [94,]  2100 37210000
#>  [95,]  4100  4410000
#>  [96,]  8100 16810000
#>  [97,]  6100 65610000
#>  [98,]  2100 37210000
#>  [99,]  4100  4410000
#> [100,]  8100 16810000
```

Este generador cae rápidamente en cilcos cortos, por ejemplo, si aparece un cero
se propagará por siempre. 

A inicios de 1950s se exploró el método y se propusieron mejoras, por ejemplo 
para evitar caer en cero. Metrópolis logró obtener una secuencia de 750,000 
números distintos al usar semillas de 38 bits (usaba sistema binario), además la
secuencia de Metrópolis mostraba propiedades deseables. No obstante, el método
del valor medio no es considerado un método bueno por lo común de los ciclos
cortos.

#### Ejemplo: rand {-}
Por muchos años (antes de 1995) el generador de la función _rand_ en Matlab fue 
el generador congruencial:
    
$$X_{n+1} = (7^5)X_n mod(2^{31}-1)$$
Construyamos sucesiones de longitud $1,500$ usando el algoritmo de _rand_:


```r
sucesion <- function(n = 1500, semilla = runif(1, 0, 2 ^ 31 - 1)){
    x <- rep(NA, n)
    u <- rep(NA, n)
    x[1] <- semilla
    u[1] <- x[1] / (2 ^ 31 - 1) # transformamos al (0, 1)
    for(i in 2:n){
        x[i] <- (7 ^ 5 * x[i - 1]) %% (2 ^ 31 - 1)
        u[i] <- x[i] / (2 ^ 31 - 1)
    }
    u
}
u_rand <- sucesion(n = 150000)
sucesiones <- map_df(1:12, ~data_frame(serie = ., sim = sucesion(), 
    ind = 1:length(sim)))
sucesiones
#> # A tibble: 18,000 x 3
#>    serie    sim   ind
#>    <int>  <dbl> <int>
#>  1     1 0.567      1
#>  2     1 0.408      2
#>  3     1 0.455      3
#>  4     1 0.0511     4
#>  5     1 0.512      5
#>  6     1 0.597      6
#>  7     1 0.296      7
#>  8     1 0.583      8
#>  9     1 0.449      9
#> 10     1 0.609     10
#> # ... with 17,990 more rows
```

Una propiedad deseable es que la sucesión de $u_i$ parezca una sucesión de 
observaciones independientes de una $Uniforme(0,1)$.

1. Veamos una gráfica del índice de simulación contra el valor obtenido


```r
ggplot(sucesiones, aes(x = ind, y = sim)) + 
  geom_point(alpha = 0.5, size = 1.5) +     # alpha controla la transparencia
  facet_wrap(~ serie) +
  geom_smooth(method = "loess", se = FALSE, color = "white", size = 0.7)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/unnamed-chunk-3-1.png" width="672" />

2. Comparemos con los cuantiles de una uniforme:


```r
ggplot(sucesiones) + 
  stat_qq(aes(sample = sim), distribution = qunif) +
  geom_abline(color = "white", size = 0.6, alpha = 0.6) +
  facet_wrap(~ serie) 
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/unnamed-chunk-4-1.png" width="672" />

#### Ejemplo: RANDU {-}
*RANDU* fue generador de números aleatorios ampliamente utilizado en los 60´s 
y 70´s, se define como:

$$X_{n + 1}= (2 ^ {16} + 3)X_n mod(2^{31})$$

A primera vista las sucesiones se asemejan a una uniforme, sin embargo, 
cuando se grafican ternas emergen patrones no deseados.


```r
library(tourr)
library(plotly)

n <- 150000 # longitud de la sucesión
x <- rep(NA, n)
u <- rep(NA, n)
 
x[1] <- 4798373 # semilla
u[1] <- x[1] / (2 ^ 31 - 1) # transformamos al (0, 1)
for(i in 2:n){
    x[i] <- ((2 ^ 16 + 3) * x[i - 1]) %% (2 ^ 31)
    u[i] <- x[i] / (2 ^ 31)
}
u_randu <- u

set.seed(8111938)
mat <- matrix(u[1:1500], ncol = 3, byrow = TRUE)
tour <- new_tour(mat, grand_tour(), NULL)
steps <- seq(0, 1, 0.01)
names(steps) <- steps
mat_xy <- map_dfr(steps, ~data.frame(center(mat %*% tour(.)$proj)), 
    .id = "steps")
# step 0.72
mat_xy %>% 
    mutate(steps = as.numeric(steps)) %>% 
    plot_ly(x = ~X1, y = ~X2, frame = ~steps, type = 'scatter', 
        mode = 'markers', showlegend = F, marker = list(size = 5, 
            color = "black"), opacity=0.5) %>% 
    animation_opts(frame = 250)
```

<!--html_preserve--><div id="htmlwidget-e078194fa30572ab909d" style="width:450px;height:480px;" class="plotly html-widget"></div>

Veamos los resultados enteros del generador, ¿qué notas?


```r
options(digits=5)
n <- 50
x <- rep(NA, n)
x[1] <- 1 # semilla
u[1] <- x[1] # transformamos al (0, 1)
for(i in 2:n){
    x[i] <- ((2 ^ 16 + 3) * x[i - 1]) %% (2 ^ 31)
}
x
#>  [1]          1      65539     393225    1769499    7077969   26542323
#>  [7]   95552217  334432395 1146624417 1722371299   14608041 1766175739
#> [13] 1875647473 1800754131  366148473 1022489195  692115265 1392739779
#> [19] 2127401289  229749723 1559239569  845238963 1775695897  899541067
#> [25]  153401569 1414474403  663781353 1989836731 1670020913  701529491
#> [31] 2063890617 1774610987  662584961  888912771 1517695625 1105958811
#> [37] 1566426833 1592415347 1899101529 1357838347 1792534561  682145891
#> [43]  844966185 1077967739 1010594417  656824147 1288046073 1816859115
#> [49] 1456223681  975544643
```

### Generadores congruenciales y Mersenne-Twister {-}
Los generadores como *rand* y *RANDU* 
($X_{n+1} = (7^5)X_n mod(2^{31}-1)$ y 
$X_{n+1}= (2 ^ {16} + 3)X_n mod(2^{31})$)
se denominan generadores congruenciales.

<div class="caja">
Los Generadores Congruenciales Lineales (GCL) tienen la forma

$$X_{n+1} = (aX_n + c)mod(m)$$
Están determinados por los parámetros:
* Módulo: $m > 0$  
* Multiplicador $0\le a < m$  
* Incremento $c \le m$  
* Semilla $0\le X_0 < m$

</div>

Los GCL se introdujeron en 1949 por D.H. Lehemer y son muy [populares](https://en.wikipedia.org/wiki/Linear_congruential_generator). 
La elección de los parámetros determina la calidad del generador:

1. Queremos $m$ grande pues el periodo (longitud del ciclo) del 
generador no puede tener más de $m$ elementos. 

2. Queremos velocidad, en este caso, un valor 
conveniente para $m$ es el *tamaño de palabra* (máximo número de bits que puede 
procesar el CPU en un ciclo) de la computadora. Los GCL más eficientes tienen 
$m$ igual a una potencia de 2 (es usual 2^32^ o 2^64^) de esta manera la 
operación módulo se calcula truncando todos los dígitos excepto los últimos 32 ó 
64 bits.

* ¿podemos elegir $a$ y $c$ de tal manera que logremos alcanzar el periodo 
máximo ($m$)?
 Un generador congruencial mixto ($c>0$) tendrá periodo completo para todas las 
 semillas sí y sólo sí:
    + $m$ y $c$ son primos relativos.  
    + $a-1$ es divisible por todos los factores primos de $m$.  
    + $a-1$ es divisible por 4 si $m$ es divisible por 4.

Vale la pena notar que un periodo grande no determina que el generador 
congruencial es *bueno*, debemos verificar que los números que generan se 
comportan como si fueran aleatorios. Los GCLs tienden a exhibir defectos, por 
ejemplo, si se utiliza un GCL para elegir puntos en un espacio de dimensión $k$ 
los puntos van a caer en a lo más $(k!m)^{1/k}$ hiperplanos paralelos $k$ 
dimensionales (como observamos con *RANDU*), donde $k$ se refiere a la dimensión 
de $[0,1]^k$. 

Los GCLs continuan siendo utilizados en muchas aplicaciones porque con una 
elección cuidadosa de los parámetros pueden pasar muchas pruebas de 
aleatoriedad, son rápidos y requiren poca memoria. Un ejemplo es Java, su
generador de números aleatorios default [java.util.Random](https://docs.oracle.com/javase/8/docs/api/java/util/Random.html)
es un GCL con multiplicador $a=25214903917$, incremento $c=11$ y 
módulo $m=2^{48}$.

, sin embargo, actualmente el
generador default de R es el Mersenne-Twister que no pertenece a la clase de 
GCLs (se puede elegir usar otros generadores para ver los disponible teclea 
?Random).

El generador **Mersenne-Twister** se desarrolló en 1997 por Makoto Matsumoto y 
Takuji Nishimura (@matsumoto), es el generador default en muchos programas, por
ejemplo, en Python, Ruby, C++ estándar, Excel y Matlab (más [aquí](https://en.wikipedia.org/wiki/Mersenne_Twister)).
Este generador tiene propiedades deseables como un periodo largo [(2^19937-1)](http://lcn2.github.io/mersenne-english-name/m19937/prime-c.html) y 
el hecho que pasa muchas pruebas de aleatoriedad. 

### Pruebas de aleatoriedad {-}
<!--If a sequence behaves randomly with respect to tests T1, T2, . . . , T, , we cannot be sure in general that it will not be a miserable failure when it is subjected to a further test T,+l; yet each test gives us more and more confidence in the randomness of the sequence. In practice, we apply about half a dozen different kinds of statistical tests to a sequence, and if it passesthese satisfactorily we consider it to be random-it is then presumed innocent until proven guilty.-->

Hasta ahora hemos graficado las secuencias de números aleatorios para evaluar 
su aleatoriedad, sin embargo, el ojo humano no es muy bueno discriminando 
aleatoriedad y las gráficas no escalan. Es por ello que resulta conveniente 
hacer pruebas estadísticas para evaluar la calidad de los generadores de números
pseudoaleatorios.

Hay dos tipos de pruebas: 

1) **empíricas**: evalúan estadísticas de sucesiones
de números.

2) **teóricas**: se establecen las características de las sucesiones usando 
métodos de teoría de números con base en la regla de recurrencia que generó la 
sucesión. 

Veremos 2 ejemplos de la primera clase:

#### Ejemplo: prueba de bondad de ajuste $\chi^2$ {-}

$H_0:$ Los datos son muestra de una cierta distribución $F$.  
$H_1:$ Los datos no son una muestra de $F$.

Procedimiento:

1. Partir el soporte de $F$ en $c$ celdas que son exhaustivas y mutuamente 
exculyentes.  
2. Contar el número de observaciones en cada celda $O_i$.  
3. Calcular el valor esperado en cada celda bajo $F$: $e_i=np_i$.  
4. Calcular la estadística de prueba:

$$\chi^2 = \sum_{i=1}^c \frac{(o_i - e_i)^2}{e_i} \sim \chi^2_{c-k-1}$$
    donde $c$ es el número de celdas y $k$ el número de parámetros estimados en 
    $F$ a partir de los datos observados.


```r
u_rand_cat <- cut(u_rand, breaks = seq(0, 1, 0.1))
u_randu_cat <- cut(u_randu, breaks = seq(0, 1, 0.1))
u_mt <- runif(150000)
u_mt_cat <- cut(u_mt, breaks = seq(0, 1, 0.1))

chisq_test <- function(o_i) {
    expected <- rep(15000, 10)
    observed <- table(o_i)
    x2 <- sum((observed - expected) ^ 2 / expected)
    list(x2 = x2, p_value = pchisq(x2, 8))
}

chisq_test(u_rand_cat)
#> $x2
#> [1] 5.8659
#> 
#> $p_value
#> [1] 0.33775
chisq_test(u_randu_cat)
#> $x2
#> [1] 3.0491
#> 
#> $p_value
#> [1] 0.068759
chisq_test(u_mt_cat)
#> $x2
#> [1] 17.288
#> 
#> $p_value
#> [1] 0.97276
```

Una variación de esta prueba de bondad de ajuste $\chi^2$, es la prueba de u
uniformidad k-dimensional:

$H_0:$ Distribución uniforme en $[0,1]^k$, con $k = 1,2,...$

En este caso se divide el espacio $[0,1]^k$ en celdas exhaustivas y mutuamente
excluyentes, y se aplica la prueba $\chi^2$ a los vectores sucesivos 
$(u_1,u_2,...,u_k),(u_{k+1},u_{k+2},...,u_{2k}),...$

#### Ejemplo: prueba de espera
$H_0:$ independencia y uniformidad

Procedimiento:  
1. Seleccionar un subintervalo del $(0,1)$.  
2. Calcular la probabilidad del subintervalo.  
3. Ubicar en la sucesión las posiciones de los elementos que pertenezcan al
subintervalo.  
4. Calcular el número de elementos consecutivos de la sucesión entre cada una
de las ocurrencias consecutivas de elementos del subintervalo (_tiempos de espera_).  
5. La distribución de los tiempos de espera es geométrica con parámetro calculado 
en 2.  
6. Aplicar una prueba $\chi^2$ a los tiempos de espera.


```r
# 1
intervalo <- c(0, 0.5)
# 2
p_intervalo <- 0.5
# 3 evaluamos u_rand
gap_u_rand <- data_frame(posicion = 1:length(u_rand), u_rand = u_rand, 
    en_intervalo = (0.5 < u_rand & u_rand < 1))
    
posiciones <- gap_u_rand %>% 
    filter(en_intervalo) %>%
    mutate(
        espera = posicion - lag(posicion), 
        espera = ifelse(is.na(espera), posicion, espera), 
        espera = factor(espera, levels = 1:18)
        ) 
# 4
gap_frec <- as.data.frame(table(posiciones$espera)) %>% 
    rename(espera = Var1, obs = Freq)
# esperados geométrica
gap_frec$geom <- (dgeom(0:18, p_intervalo) * length(u_rand)) [-1]
x2 <- sum((gap_frec$obs - gap_frec$geom) ^ 2 / gap_frec$geom)
pchisq(x2, 16)
#> [1] 0.7772
```

Otras pruebas de aleatoriedad son _prueba de rachas_, _Kolmogorov-Smirnov_, 
_prueba de poker_, puedes leer más de generadores aleatorios y pruebas en 
@knuth. En R hay pruebas implementadas en los paquetes `randtoolbox`y 
`RDieHarder`.

## Variables aleatorias
El segundo requisito práctico de un proyecto de simulación es: 

* Transformar los números aleatorios en variables de entrada del modelo (e.g. generación de muestras con cierta distribución).

En nuestro caso, usamos simulación aplicada a modelos estadísticos:

Un modelo estadístico $F$ es un conjuto de distribuciones (o densidades o 
funciones de regresión). Un **modelo paramétrico** es un conjunto $F$ que puede 
ser parametrizado por un número finito de parámetros. 

Por ejemplo, si suponemos que los datos provienen de una distribución Normal, el
modelo es:

$$F=\bigg\{p(x;\mu, \sigma) = \frac{1}{\sigma\sqrt{2\pi}}exp\bigg(-\frac{1}{2\sigma^2}(x-\mu)^2\bigg), \mu \in \mathbb{R}, \sigma>0\bigg\}$$

En general, un modelo paramétrico tiene la forma

$$F=\bigg\{p(x;\theta):\theta \in \Theta \bigg\}$$

donde $\theta$ es un parámetro desconocido (o un vector de parámetros) que 
puede tomar valores en el espacio paramétrico $\Theta$. 

En lo que resta de esta sección estudiaremos simulación de modelos paramétricos.
Comencemos repasando algunos conceptos:

<div class="caja">
Una **variable aleatoria** es un mapeo entre el espacio de resultados y los 
números reales.
</div>

Ejemplo. Lanzamos una moneda justa dos veces, definimos $X$ como en el número de
soles, entonces la variable aleatoria se pueden resumir como:

$\omega$ |$P(\{\omega\})$|$X(\omega)$
---------|--------------|-----------
AA       |1/4           |0
AS       |1/4           |1
SA       |1/4           |1
SS       |1/4           |2


<div class = "caja">
La **función de distribución acumulada** es la función $P_X:\mathbb{R}\to[0,1]$
definida como:
$$P_X(x)=P(X\leq x)$$
</div>

<br/>

En el ejemplo:
$$
P_X(x) = \left\{
  \begin{array}{lr}
    0   &  x < 0\\
    1/4 &  0 \leq x < 1 \\
    3/4 &  1 \leq x < 2 \\ 
    1 &  x \ge 2
  \end{array}
\right.
$$

<div class = "caja">
Una variable aleatoria $X$ es **discreta** si toma un número contable de valores 
$\{x_1,x_2,...\}$. En este caso definimos la función de probabilidad o la 
función masa de probabilidad de X como $p_X(x)=P(X=x)$.  
</div>

Notemos que $p_X(x)\geq 0$ para toda $x \in \mathbb{R}$ y $\sum_i p_X(x)=1$. Más
aún, la función de distribución acumulada esta relacionada con $p_X$ por
$$P_X(x)=P(X \leq x)= \sum_{x_i\leq x} = \sum_{x_i\leq x}p_{X}(x_i)$$

$$
p_X(x) = \left\{
  \begin{array}{lr}
    1/4 &  x = 0 \\
    1/2 &  x = 1 \\ 
    1/4 &  x = 2\\
    0   &  e.o.c.
  \end{array}
\right.
$$

<div class = "caja">

Sea $X$ una variable aleatoria con FDA $P_X$. La función de distribución
acumulada inversa o **función de cuantiles** se define como:
$$P_X^{-1}(q) = inf\{x:P_X(x)>q\}$$
para $q \in [0,1]$.
</div>

Llamamos a $P^{-1}(1/4)$ el primer cuartil, a $P^{-1}(1/2)$ la mediana y 
$P^{-1}(3/4)$ el tercer cuartil.

### Familias discretas importantes {-}

Muchas variables aleatorias provienen de familias o tipos de experimentos 
similares lo que nos ahorra tener que determinar las funciones de distribución y 
sus propiedades cada vez. 

Por ejemplo, el resultado de interés en muchos experimentos es un resultado que
solo puede tomar dos valores: una moneda puede ser águila o sol, un persona 
puede estar empleada o desempleada, un transistor puede estar defectuoso o no,...
La misma distribución de probabilidad que describe a una variable aleatoria que 
puede tomar el valor de $1$ con probabilidad $p$ y $0$ con probabilidad $q=1-p$, 
se conoce como distribución Bernoulli.

#### Distribución Bernoulli {-}
Sea $X$ la variable aleatoria que representa un lanzamiento de moneda, con
$P(X=1)=p$ y $P(X=0)=1-p$ para alguna $p\in[0,1].$ Decimos que $X$ tiene una 
distribución Bernoulli ($X \sim Bernoulli(p)$), y su función de distribución
es:

$$
p(x) = \left\{
  \begin{array}{lr}
    p^x(1-p)^{1-x}   &  x \in \{0,1\}\\
    0 & e.o.c. \\
  \end{array}
\right.
$$

$E(X) = p, Var(X)=p(1-p)$

Notemos que un experimento Bernoulli es una repetición del experimento que 
involucra solo dos posibles salidas. Es común que nos interese el resultado
de la repetición de experimentos Bernoulli independientes, en este caso se usa
la distribución Binomial.

#### Distribución Binomial {-}
Supongamos que tenemos una moneda que cae en sol con probabilidad $p$, para 
alguna $p\in[0,1].$ Lanzamos la moneda $n$ veces y sea $X$ el número de
soles. Suponemos que los lanzamientos son independientes, entonces la función
de distribución es:

$$
p(x) = \left\{
  \begin{array}{lr}
     {n \choose x}p^x(1-p)^{n-x}   & x \in \{0,1,...,n\}\\
    0 &  e.o.c. \\
  \end{array}
\right.
$$

$E(X) = np, Var(X)=np(1-p)$ 

Si $X_1 \sim Binomial(n_1, p)$ y $X_2 \sim Binomial(n_2,p)$ entonces
$X_1 + X_2 \sim Binomial(n_1+n_2, p)$. En general la distribución binomial 
describe el comportamiento de una variable $X$ que cuenta número de 
_éxitos_ tal que: 1) el número de observaciones $n$ esta fijo, 2) cada 
observación es independiente, 3) cada observación representa uno de dos posibles 
eventos (_éxito_ o _fracaso_) y 3) la probabilidad de éxito $p$ es la misma en 
cada observación.


```r
densidades <- ggplot(data.frame(x = -1:20)) +
  geom_point(aes(x = x, y = dbinom(x, size = 20, prob = 0.5), color = "n=20;p=0.5"), 
      show.legend = FALSE) +
  geom_path(aes(x = x, y = dbinom(x, size = 20, prob = 0.5), color = "n=20;p=0.5"), 
      alpha = 0.6, linetype = "dashed", show.legend = FALSE) + 
  geom_point(aes(x = x, y = dbinom(x, size = 20, prob = 0.1), color = "n=20;p=0.1"), 
      show.legend = FALSE) +
  geom_path(aes(x = x, y = dbinom(x, size = 20, prob = 0.1), color = "n=20;p=0.1"), 
      alpha = 0.6, linetype = "dashed", show.legend = FALSE) + 
  labs(color = "", y  = "", title = "Distribución binomial")

dists <- ggplot(data_frame(x = -1:20), aes(x)) + 
    stat_function(fun = pbinom, args = list(size = 20, prob = 0.5), 
        aes(colour = "n=20;p=0.5"), alpha = 0.8) +
    stat_function(fun = pbinom, args = list(size = 20, prob = 0.1), 
        aes(colour = "n=20;p=0.1"), alpha = 0.8) +
    labs(y = "", title = "FDA", color = "")

grid.arrange(densidades, dists, ncol = 2, newpage = FALSE)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/graficas_binomial-1.png" width="864" />


#### Distribución Uniforme {-}
Decimos que $X$ tiene una distribución uniforme en $\{a,...,b\}$ ($a,b$ enteros) 
si tiene una función de probailidad dada por:

$$
p(x) = \left\{
  \begin{array}{lr}
    1/n   &  x \in \{a,...,b\}\\
    0 &  e.o.c. \\
  \end{array}
\right.
$$
donde $n = b-a+1$,
$E(X) = (a+b)/2, Var(X)=(n^2-1)/12$

El ejemplo más común es el lanzamiento de un dado.

#### Distribución Poisson {-}
$X$ tienen una distribución Poisson con parámetro $\lambda$ si 
$$
p(x) = \left\{
  \begin{array}{lr}
    e^{-\lambda} \frac{\lambda^x}{x!}  & x \in \{0,1,...\}\\
    0 &  e.o.c. \\
  \end{array}
\right.
$$

$E(X) = \lambda, Var(X)=\lambda$

<br/>

La distribución Poisson se utiliza con frecuencia para modelar conteos de 
eventos raros, por ejemplo número de accidentes de tráfico. La distribución 
Poisson es un caso límite de la distribución binomial cuando el número de casos 
es muy grande y la probabilidad de éxito $p$ es chica.

Una propiedad de la distribución Poisson es:
$X_1 \sim Poisson(\lambda_1)$ y $X_2 \sim Poisson(\lambda_2)$ entonces 
$X_1 + X_2 \sim Poisson(\lambda_1 + \lambda_2)$.


```r
densidades <- ggplot(data.frame(x = -1:20)) +
  geom_point(aes(x = x, y = dpois(x, lambda = 4), color = "lambda=4"), show.legend = FALSE) +
  geom_path(aes(x = x, y = dpois(x, lambda = 4), color = "lambda=4"), 
      alpha = 0.6, linetype = "dashed", show.legend = FALSE) + 
  geom_point(aes(x = x, y = dpois(x, lambda = 10), color = "lambda=10"), show.legend = FALSE) +
  geom_path(aes(x = x, y = dpois(x, lambda = 10), color = "lambda=10"), 
      alpha = 0.6, linetype = "dashed", show.legend = FALSE) +
  labs(color = "", y = "", title = "Distribución Poisson")

dists <- ggplot(data_frame(x = -1:20), aes(x)) + 
    stat_function(fun = ppois, args = list(lambda = 4), 
        aes(colour = "lambda=4"), alpha = 0.8) +
    stat_function(fun = ppois, args = list(lambda = 10), 
        aes(colour = "lambda=10"), alpha = 0.8) +
    labs(y = "", title = "FDA", color = "")

grid.arrange(densidades, dists, ncol = 2, newpage = FALSE)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/graficas_poisson-1.png" width="864" />

#### Distribución geométrica {-}
$X$ tiene distribución geométrica con parámetro $p \in (0,1)$, $X \sim Geom(p)$
si, 
$$
p(x) = \left\{
  \begin{array}{lr}
    p(1-p)^{k-1}  & x \in \{1,2,...\}\\
    0 &  e.o.c. \\
  \end{array}
\right.
$$

$E(X)=1/p, Var(X)=(1-p)/p^2$
<br/>
con $k \geq 1$. Podemos pensar en $X$ como el número de lanzamientos necesarios
hasta que obtenemos el primer sol en los lanzamientos de una moneda.


```r
densidades <- ggplot(data.frame(x = -1:20)) +
  geom_point(aes(x = x, y = dgeom(x, p = 0.5), color = "p=0.5"), show.legend = FALSE) +
  geom_path(aes(x = x, y = dgeom(x, p = 0.5), color = "p=0.5"), show.legend = FALSE, 
      alpha = 0.6, linetype = "dashed") + 
  geom_point(aes(x = x, y = dgeom(x, p = 0.1), color = "p=0.1"), show.legend = FALSE) +
  geom_path(aes(x = x, y = dgeom(x, p = 0.1), color = "p=0.1"), 
      show.legend = FALSE, alpha = 0.6, linetype = "dashed") +
  labs(title = "Distribución geométrica", y = "")

dists <- ggplot(data_frame(x = -1:20), aes(x)) + 
    stat_function(fun = pgeom, args = list(p = 0.5), 
        aes(colour = "p=0.5"), alpha = 0.8) +
    stat_function(fun = pgeom, args = list(p = 0.1), 
        aes(colour = "p=0.1"), alpha = 0.8) +
    labs(y = "", title = "FDA", color = "")

grid.arrange(densidades, dists, ncol = 2, newpage = FALSE)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/graficas_geometrica-1.png" width="864" />


#### Variables aleatorias continuas {-}

<div class = "caja">
Una variable aleatoria $X$ es **continua** si existe una función $p_x$ tal que
$p_X(x) \geq 0$ para toda $x$, $\int_{-\infty}^{\infty}p_X(x)dx=1$ y para toda 
$a\leq b$,

$$P(a < X < b) = \int_{a}^b p_X(x)dx$$

La función $p_X(x)$ se llama la **función de densidad de probabilidad** (fdp). 
Tenemos que 
$$P_X(x)=\int_{-\infty}^x p_X(t)dt$$
y $p_X(x)=P_X^{\'}(x)$ en todos los puntos $x$ en los que la FDA $P_X$ es 
diferenciable.
</div>
<br/>

Ejemplo. Supongamos que elegimos un número al azar entre cero y uno, entonces

$$
p(x) = \left\{
  \begin{array}{lr}
    \frac{1}{b-a}  & x \in [0, 1]\\
    0 &  e.o.c. \\
  \end{array}
\right.
$$
es claro que $p_X(x) \geq 0$ para toda $x$ y $\int_{-\infty}^{\infty}p_X(x)dx=1$, 
la FDA esta dada por 

$$
P_X(x) = \left\{
  \begin{array}{lr}
    0 & x < 0 \\ 
    x  & x \in [0,1]\\
    1 &  x>b \\
  \end{array}
\right.
$$

Vale la pena notar que en el caso de variables aleatorias continuas $P(X=x)=0$
para toda $x$ y pensar en $p_X(x)$ como $P(X=x)$ solo tiene sentido en el caso
discreto.
<br/>

### Familias Continuas importantes {-}

#### Distribución Uniforme {-}
$X$ tiene una distribución $Uniforme(a,b)$ si 

$$
p(x) = \left\{
  \begin{array}{lr}
    \frac{1}{b-a}  & x \in [a,b]\\
    0 &  e.o.c. \\
  \end{array}
\right.
$$

donde $a < b$. La función de distribución acumualda es 

$$
P_X(x) = \left\{
  \begin{array}{lr}
    0 & x < a \\ 
    \frac{x-a}{b-a}  & x \in [a,b]\\
    1 &  x>b \\
  \end{array}
\right.
$$

$E(X) = (a+b)/2, Var(X)= (b-a)^2/12$

<br/>



```r
densidades <- ggplot(data_frame(x = c(-5 , 5)), aes(x)) + 
    stat_function(fun = dunif, aes(colour = "a=0; b=1"), show.legend = FALSE) +
    stat_function(fun = dunif, args = list(min = -5, max = 5), aes(colour = "a=-5; b=5"), show.legend = FALSE) +
    stat_function(fun = dunif, args = list(min = 0, max = 2), aes(colour = "a=0; b=2"), show.legend = FALSE) +
    labs(y = "", title = "Distribución uniforme", colour = "")

dists <- ggplot(data_frame(x = c(-5 , 5)), aes(x)) + 
    stat_function(fun = punif, aes(colour = "a=0; b=1"), show.legend = FALSE) +
    stat_function(fun = punif, args = list(min = -5, max = 5), aes(colour = "a=-5; b=5"), show.legend = FALSE) +
    stat_function(fun = punif, args = list(min = 0, max = 2), aes(colour = "a=0; b=2"), show.legend = FALSE) +
    labs(y = "", title = "FDA")

grid.arrange(densidades, dists, ncol = 3, newpage = FALSE)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/graficas_uniformes-1.png" width="864" />

#### Distribución Normal {-}
$X$ tiene una distribución normal con parámetros $\mu$ y $\sigma$, denotado
$X\sim N(\mu, \sigma^2)$ si

$$p(x) = \frac{1}{\sigma\sqrt{2\pi}}exp\bigg(-\frac{1}{2\sigma^2}(x-\mu)^2\bigg)$$

$E(X)=\mu, Var(X)=\sigma^2$

<br/>
donde $\mu \in \mathbb{R}$ y $\sigma>0$. 

Decimos que $X$ tiene una distribución **Normal estándar** si $\mu=0$ y 
$\sigma=1$. Una variable aleatoria Normal estándar se denota tradicionalmente 
por $Z$, su función de densidad de probabilidad por $\phi(z)$ y la función de 
probabilidad acumulada por $\Phi(z)$.

Algunas porpiedades importantes son:

1. Si $X \sim N(\mu, \sigma^2)$, entonces $Z=(X-\mu)/\sigma \sim N(0,1)$.  

2. Si $Z \sim N(0, 1)$ entonces $X = \mu + \sigma Z \sim N(\mu, sigma^2)$.

3. Si $X_i \sim N(\mu_i, \sigma_i^2)$, $i=1,...,n$ independientes, entonces:
$$\sum_{i=1}^n X_i \sim N(\sum_{i=1}^n \mu_i, \sum_{i=1}^n \sigma_i^2)$$

4. Se sigue de 1 que si $X\sim N(\mu, \sigma^2)$, entonces
$$P(a<X<b) = P\big(\frac{a-\mu}{\sigma} < Z < \frac{b-\mu}{\sigma}\big)= \Phi\big(\frac{b-\mu}{\sigma}\big) - \Phi\big(\frac{a-\mu}{\sigma}\big)$$


```r
densidades <- ggplot(data_frame(x = c(-5 , 5)), aes(x)) + 
    stat_function(fun = dnorm, aes(colour = "m=0; s=1"), show.legend = FALSE) +
    stat_function(fun = dnorm, args = list(mean = 1), aes(colour = "m=1; s=1"), show.legend = FALSE) +
    stat_function(fun = dnorm, args = list(sd = 2), aes(colour = "m=1; s=2"), show.legend = FALSE) +
    labs(y = "", title = "Distribución Normal", colour = "")

dists <- ggplot(data_frame(x = c(-5 , 5)), aes(x)) + 
    stat_function(fun = pnorm, aes(colour = "m=0; s=1"), show.legend = FALSE) +
    stat_function(fun = pnorm, args = list(mean = 1), aes(colour = "m=1; s=1"), show.legend = FALSE) +
    stat_function(fun = pnorm, args = list(sd = 2), aes(colour = "m=1; s=2"), show.legend = FALSE) +
    labs(y = "", title = "FDA")

cuantiles <- ggplot(data_frame(x = c(0, 1)), aes(x)) + 
    stat_function(fun = qnorm, aes(colour = "m=0; s=1")) +
    stat_function(fun = qnorm, args = list(mean = 1), aes(colour = "m=1; s=1")) +
    stat_function(fun = qnorm, args = list(sd = 2), aes(colour = "m=1; s=2")) +
    labs(y = "", title = "Funciones de cuantiles", colour = "")

grid.arrange(densidades, dists, cuantiles, ncol = 3, newpage = FALSE)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/graficas_normales-1.png" width="1008" />

#### Distribución Exponencial {-}
Una variable aleatoria $X$ tienen distribución Exponencial con parámetro $\beta$,
$X \sim Exp(\beta)$ si, 

$$
p(x) = \left\{
  \begin{array}{lr}
    \frac{1}{\beta}e^{-x/\beta}  & x >0\\
    0 &  e.o.c. \\
  \end{array}
\right.
$$

$E(X)=\beta, Var(X)=\beta^2$
<br/>

donde $\beta > 0$. La distribución exponencial se utiliza para modelar tiempos
de espera hasta un evento, por ejemplo modelar el tiempo de vida de un 
componente electrónico o el tiempo de espera entre llamadas telefónicas.

#### Distribución Gamma {-}
* Comencemos definiendo la **función Gamma**: para $\alpha>0$, 
$\Gamma(\alpha)=\int_0^{\infty}y^{\alpha-1}e^{-y}dy$, esta función es una 
extensión de la función factorial, tenemos que si $n$ es un entero positivo,
$\Gamma(n)=(n-1)!$.  

* Ahora, $X$ tienen una **distribución Gamma** con parámetros $\alpha$, $\beta$, 
denotado como $X \sim Gamma(\alpha, \beta)$ si 

$$
p(x) = \left\{
  \begin{array}{lr}
    \frac{1}{\beta^\alpha \Gamma(\alpha)}x^{\alpha-1}e^{-x/\beta}  & x >0\\
    0 &  e.o.c. \\
  \end{array}
\right.
$$
$E(X)=\alpha \beta, Var(X)=\alpha \beta^2$
<br/>

* Vale la pena notar que una distribución exponencial es una $Gamma(1, \beta)$. 

* Una propiedad adicional es que si $X_i \sim Gamma(\alpha_i, \beta)$ 
independientes, entonces 
$\sum_{i=1}^n X_i \sim Gamma(\sum_{i=1}^n \alpha_i, \beta)$. 

En la práctica la distribución Gamma se ha usado para modelar el tamaño de 
las reclamaciones de asegurados, en neurociencia se ha usado para describir la
distribución de los intervalos entre los que ocurren picos. Finalmente, 
la distribución Gamma es muy usada en estadística bayesiana como a priori
conjugada para el parámetro de precisión de una distribución Normal.



```r
densidades <- ggplot(data_frame(x = c(0 , 12)), aes(x)) + 
    stat_function(fun = dgamma, args = list(shape = 1), aes(colour = "a=1;b=1"), show.legend = FALSE) +
    stat_function(fun = dgamma, args = list(scale = 0.5, shape = 2), aes(colour = "a=2;b=0.5"), show.legend = FALSE) +
    stat_function(fun = dgamma, args = list(scale = 3, shape = 4), aes(colour = "a=4,b=3"), show.legend = FALSE) +
    labs(y = "", title = "Distribución Gamma", colour = "")

dists <- ggplot(data_frame(x = c(0 , 12)), aes(x)) + 
    stat_function(fun = dgamma, args = list(shape = 1), aes(colour = "a=1;b=1")) +
    stat_function(fun = dgamma, args = list(scale = 0.5, shape = 2), aes(colour = "a=2;b=0.5")) +
    stat_function(fun = dgamma, args = list(scale = 3, shape = 4), aes(colour = "a=4,b=3")) +
    labs(y = "", title = "FDA", color="")

grid.arrange(densidades, dists, ncol = 2, newpage = FALSE)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/graficas_gamma-1.png" width="864" />


#### Distribución Beta {-}

* $X$ tiene una distrinución Beta con parámetros $\alpha > 0$ y $\beta >0$, 
$X \sim Beta(\alpha, \beta)$ si 

$$
p(x) = \left\{
  \begin{array}{lr}
    \frac{\Gamma(\alpha+\beta)}{\Gamma(\alpha)\Gamma(\beta)}x^{\alpha-1}(1-x)^{\beta-1}  & 0 < x < 1\\
    0 &  e.o.c. \\
  \end{array}
\right.
$$
$E(X)=\alpha/(\alpha+\beta), Var(X)=\alpha \beta /[(\alpha+\beta)^2(\alpha + \beta + 1)]$

<br/>
La distribución Beta se ha utilizado para describir variables aleatorias 
limitadas a intervalos de longitud finita, por ejemplo, distribución del tiempo
en sistemas de control o administración de proyectos, proporción de minerales
en rocas, etc. 


```r
densidades <- ggplot(data_frame(x = c(0 , 1)), aes(x)) + 
    stat_function(fun = dbeta, args = list(shape1 = 2, shape2 = 2), 
        aes(colour = "a=2; b=2"), show.legend = FALSE) +
    stat_function(fun = dbeta, args = list(shape1 = 5, shape2 = 2), 
        aes(colour = "a=5; b=2"), show.legend = FALSE) +
    stat_function(fun = dbeta, args = list(shape1 = .5, shape2 = .5), 
        aes(colour = "a=.5; b=.5"), show.legend = FALSE) +
    labs(y = "", title = "Distribución Beta", colour = "")

dists <- ggplot(data_frame(x = c(0 , 1)), aes(x)) + 
    stat_function(fun = pbeta, args = list(shape1 = 2, shape2 = 2), 
        aes(colour = "a=2; b=2")) +
    stat_function(fun = pbeta, args = list(shape1 = 5, shape2 = 2), 
        aes(colour = "a=5; b=2")) +
    stat_function(fun = pbeta, args = list(shape1 = .5, shape2 = .5), 
        aes(colour = "a=.5; b=.5")) +
    labs(y = "", title = "FDA", color="")

grid.arrange(densidades, dists, ncol = 2, newpage = FALSE)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/graficas_beta-1.png" width="864" />

## Simulación de variables aleatorias

Veremos métodos generales para simular muestras de distribuciones univariadas, 
generales se refiere a que se pueden utilizar independientemente de la forma
de la función de densidad.

Para utilizar estos métodos debemos tener un generador de números aleatorios
confiable, pues la mayoría de los métodos consisten en una transformación
de números aleatorios.

### Variables aletaorias discretas {-}

#### Método de Inversión {-}
Supongamos que deseamos generar el valor de una variable aleatoria discreta $X$
con función de probabilidad:
$$P(X=x_j) = p_j$$
con $j=0,1,2,..$.

Para lograr esto generamos un número aleatorio $U$, esto es 
$U\sim Uniforme(0,1)$ y definimos

$$
X = \left\{
  \begin{array}{lr}
    x_0   &  U < p_0\\
    x_1 &  p_0 \leq U < p_0 + p_1\\
    \vdots &\\
    x_j & \sum_{i=0}^{j-1}p_i \leq U < \sum_{i=0}^j p_i \\
    \vdots & \\
  \end{array}
\right.
$$

Como  para $0<a<b<1$ tenemos que $P(a\leq U < b)=b-a$, tenemos que 
$$P(X=x_j)=P\bigg\{\sum_{i=0}^{j-1}p_i \leq U < \sum_{i=0}^{j}p_i \bigg \}=p_j$$
y por tanto $X$ tiene la distribución deseada.

<div class = "caja">
**Método de inversión**  

1. Genera un número aleatorio $U$, tal que $U \in (0,1)$.  
   Si $U<p_0$ define $X=x_0$ y para.  
   Si $U< p_0+p_1$ define $X = x_1$ y para.  
   Si $U < p_0 + p_1 + p_2$ define $X=x_2$ y para.  
   $\vdots$

2. Si las $x_i$, están ordenadas de tal manera que $x_0<x_1<x_2<\cdots$ y si
denotamos por $P$ la función de distribución acumulada de $X$, entonces 
$P(x_k)=\sum_{i=0}^kp_i$ y por tanto, $X$ será igual a $x_j$ si 
$$P(x_{j-1}) \leq U \leq P(x_j)$$

En otras palabras, tras generar un número aleatorio $U$ determinamos el valor de
$X$ encontrando el intervalo $[P(x_{j-1}),P(x_j))$ en el que cae $U$, esto es 
equivalente a encontrar la inversa de $P(U)$.

</div>

El tiempo que uno tarda en generar una variable aleatoria discreta usando el 
método de arriba es proporcional al número de intervalos que uno debe buscar, 
es por esto que en ocasiones vale la pena considerar los posibles valores 
$x_j$ en orden decreciente de $p_j$.

![](imagenes/manicule2.jpg)  Utiliza la función runif de R y el método de 
inversión para generar 1000 simulaciones de una variable aleatoria $X$ tal que 
$p_1=0.20, p_2= 0.15, 
p_3=0.25, p_4=0.40$ donde $p_j=P(X=j)$.


```r
inv_ej <- function(n = 1000){
    sims_unif <- runif(n)
    sims_x <- case_when( 
        sims_unif < 0.2 ~ 1, 
        sims_unif < 0.35 ~ 2,
        sims_unif < 0.6 ~ 3,
        TRUE ~ 4
        )
}
sims <- inv_ej()
100 * prop.table(table(sims))
#> sims
#>    1    2    3    4 
#> 19.0 15.4 24.6 41.0
```

#### Ejemplos {-} 
**Uniforme discreta**. Supongamos que deseamos simular de una variable aleatoria 
uniforme discreta que toma valores $1,...,k$, usando los resultados anteriores 
tenemos que:

$X=j$ si $\frac{j-1}{n} \leq U < \frac{j}{n}$

Entonces $X=[kU] + 1$, donde $[x]$ representa la parte entera de x.


```r
# uniforme discreta: donde n es el número de simulaciones y k el número de elementos
runifD <- function(n = 1, k) floor(k * runif(n)) + 1
# veamos un histograma de 1000 simulaciones de una distribución Uniforme
# discreta con parámetro k = 20
x <- runifD(n = 1000, k = 20)
qplot(x, binwidth = 1)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/unnamed-chunk-9-1.png" width="300px" />

También podmeos usar la función sample de R:


```r
qplot(sample(1:20, size = 1000, replace= TRUE), binwidth = 1)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/unnamed-chunk-10-1.png" width="300px" />

**Poisson**: la clave para usar el método de la transformación inversa en este 
ejemplo es notar que:
$$p_{i+1}=\frac{\lambda}{i+1}p_i$$

donde $p_i=P(X=i) = e^{-\lambda} \cdot \lambda^i/i!$, con $i=0,1,...$. Ahora, 
la cantidad $i$ se refiere al valor que estamos considerando, $p=p_i$ es la 
probabilidad de $X = i$ y $P=P(i)$ es la probabilidad de $X\leq i$. Entonces, 
para generar una observación sequimos los siguientes pasos:

1. Generar un número aleatorio $U$, tal que $U \in (0,1)$.  
2. Inicializar: $i=0$, $p=e^{-\lambda}$, $F=p$.  
3. Si $U<F$, definir $X=i$ y parar.  
4. $p=\lambda p/(i+1)$, $F=F+p$, $i=i+1$.  
5. Volver a 3.


```r
# Poisson usando Inversión
rpoisI <- function(lambda = 1){
  U <- runif(1)
  i <- 0
  p <- exp(-lambda)
  P <- p
  while(U >= P){
    p <- lambda * p / (i + 1)
    P <- P + p
    i <- i + 1
  }
  i
}
sims_pois <- rerun(1000, rpoisI()) %>% flatten_dbl()

qplot(sims_pois, binwidth = 1)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/unnamed-chunk-11-1.png" width="300px" />

El algoritmo que propusimos verifica de manera sucesiva si el valor es 0, 1, etc.
por lo que el número de comparaciones necesarias será uno más que el valor de 
la variable. Ahora, el valor esperado de una variable aleatoria Poisson es 
$\lambda$ por lo que en promedio se harían $1+\lambda$ busquedas. Cuando $\lambda$
es grande se puede mejorar el algoritmo buscando primero en valores cercanos a
$\lambda$.

![](imagenes/manicule2.jpg) Escribe una función en R que genere simulaciones
de una variable aleatoria Poisson de la siguiente manera: define $I=[\lambda]$,
y usa que $p_{i+1}=\lambda p_i /(i+1)$ para determinar $F$ de manera recursiva. 
Genera un número aleatorio $U$, determina si $X \leq I$ comparando si 
$U \leq F(I)$. Si $X \leq I$ busca hacia abajo comenzando en $I$, de lo contrario
busca hacia arriba comenzando por $I+1$. Compara el tiempo que tardan los dos 
algoritmos en 5000 simulaciones de una variable aleatoria Poisson con 
parámetro $\lambda=10, 200, 500$.


### Aceptación y rechazo {-}
Supongamos que tenemos un método eficiente para generar simulaciones de una
variable aleatoria con función de probabilidad masa $\{q_j, j\geq 0\}$, podemos
usarla como la base para simular de una distribución que tiene función de 
probabilidad masa $\{p_j, j \geq 0\}$, para hacer esto comenzamos simulando 
una variable aleatoria $Y$ con función $\{q_j\}$ y después aceptamos o 
rechazamos el valor simulado con una probabilidad proporcional a $p_Y/q_Y$. 
En particular, sea $c$ una constante tal que 
$$\frac{p_j}{q_j}\leq c$$
para toda $j$ con $p_j > 0$. Entonces el método de aceptación y rechazo para
simular una variable aleatoria $X$ con función masa de probabilidad $p_j=P(x=j)$
es como sigue:

<div class = "caja">
**Método de aceptación y rechazo**

1. Simula el valor de $Y$, con función de probabilidad masa $q_j$.  
2. Genera un número aleatorio $U$, tal que $U \in (0,1)$.  
3. Si $U < p_y/(cq_y)$ definimos $X=Y$ y paramos, en otro caso regresamos a 1.  

En promedio este algoritmo requiere $c$ iteraciones para obtener un valor 
generado para $X$.
</div>

![](imagenes/manicule2.jpg). Supongamos que queremos simular el valor de una 
variable aleatoria $X$ que toma uno de los valores $1,2,3,4$ con probabilidades 
$p_1=0.20, p_2= 0.15, p_3=0.25, p_4=0.40$ donde $p_j=P(X=j)$. Usemos el método 
de aceptación y rechazo con $q$ la densidad uniforme en $1,...,4$. ¿Cómo se 
compara en velocidad con la función que implementaste usando el método de la 
transformación inversa?


```r
sim_acept_rechazo <- function() {
  c <- 0.4 / 0.25
  p_j <- c(0.2, 0.15, 0.25, 0.4)
  prop <- floor(4 * runif(1)) + 1
  while(runif(1) > p_j[prop] / (c * 0.25)){
      prop <- floor(4 * runif(1)) + 1
  }
  prop
}
sims <- rerun(1000, sim_acept_rechazo()) %>% flatten_dbl()
100 * prop.table(table(sims))
#> sims
#>    1    2    3    4 
#> 21.9 15.3 24.4 38.4
```





### Variables aleatorias continuas

#### Método de inversión {-}


<div class = "caja">
Sea $U$ una variable aleatoria con ditribución $U(0,1)$. Para cualquier 
función de distribución $F_X$ ($F_X$ es creciente y continua entonces existe
$F_X^{-1}$) la variable aleatoria $X$ definida como:
$$X = F_X^{-1}(U)$$
tiene distribución $F_X$.   
</div>

La proposición anterior nos da un camino para simular variables aleatorias 
continuas:

1. Generamos un número aleatorio $U$.  
2. Definimos $X = F^{-1}(U)$:


```r
ggplot(data_frame(x = c(-2 , 2)), aes(x)) + 
    geom_hline(yintercept = 0, color = "gray") +
    geom_vline(xintercept = 0, color = "gray") +
    stat_function(fun = qnorm, aes(color = "fq")) +
    stat_function(fun = dnorm, aes(color = "fdp")) +
    stat_function(fun = pnorm, aes(color = "fda")) +
    coord_fixed() +
    labs(color = "", title = "Método de transformación inversa caso Normal")
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/inversion_normal-1.png" width="480" />

#### Ejemplo: Exponencial {-}

Si $X$ es una variable aleatoria exponencial con tasa 1, entonces

$$F(x)=1-e^{-x}$$

Si definimos $x=F^{-1}(u)$, entonces
$$u=F(x)=1-e^{-x}$$
o 
$$x = -log(1-u)$$

Vale la pena notar que si $U$ tiene distribución $U(0,1)$, $1-U$ también
se distribuye uniforme(0,1).


```r
simExp <- function(){
  u <- runif(1)
  x <- -log(u)
}
```


Notemos que para cualquier constante positiva $c$, $cX$ tiene distribución 
exponencial con media $c$, por tanto una variable aleatoria exponencial con 
parámetro $\beta$ se puede generar de la siguiente manera:

$$X=-\beta log(U)$$


```r
sim_exp_beta <- function(beta, n = 1000){
  -beta * log(runif(n))
}
sims_exp <- sim_exp_beta(2)
mean(sims_exp)
#> [1] 2.0487
ggplot() + 
  geom_histogram(aes(x = sims_exp, y = ..density..), binwidth = 0.7)
```

<img src="06-v_aleatorias_y_simualcion_files/figure-html/unnamed-chunk-15-1.png" width="672" />

El algoritmo anterior también provee una manera de generar variables aleatorias
Poisson. Para esto usamos la relación entre la Poisson y la exponencial:

* Un proceso Poisson con tasa $\lambda$ resulta cuando los tiempos de espera 
entre eventos sucesivos son exponenciales independientes con parámetro $\beta$,

* Para este proceso, $N(1)$, el número de eventos en el tiempo 1 se distribuye 
Poisson con media $1/\beta$. 

* Si denotamos por $X_i$ los tiempos entre eventos, el $n$-ésimo evento 
ocurrirá en el tiempo $\sum_{i=1}^n X_i$ y por tanto el número de eventos
al tiempo 1 se puede expresar como:

$$N(1)=max\bigg\{n: \sum_{i=1}^nX_i \leq 1\bigg\}$$

* Esto es, el número de eventos al tiempo 1 es igual a la $n$ mayor para la cual 
el n-ésimo evento ocurrió al tiempo 1. 

* Por ejemplo, si el cuarto evento ocurrió al tiempo uno pero el quinto no, 
habría 4 eventos al tiempo 1. Por tanto podemos generar una variable aleatoria 
Poisson con media $\lambda = 1/\beta$ generando números aleatorios 
$U_1,...U_n,...$ y definiendo

$$N=max\bigg\{n: \sum_{i=1}^n -\beta log(U_i) \le 1\bigg\}$$
$$=max\bigg\{n: \sum_{i=1}^n -1/\lambda log(U_i) \le 1\bigg\}$$
$$=max\bigg\{n:\sum_{i=1}^n log(U_i)\geq -\lambda \bigg\}$$
$$=max\{n:log(U_1\cdot\cdot\cdot U_n) \geq -\lambda\}$$
$$=max\{n: U_1\cdot \cdot \cdot U_n \geq e^{-\lambda}\}$$

Es así, que una variable aleatoria Poisson con media $\lambda$ se puede generar
a partir de una sucesión de números aleatorios, generando números hasta que
el producto sea menor a $e^{-\lambda}$ y definiendo $X$ como uno menos del 
número de números aleatorios requeridos.

$$N = min\{n: U_1\cdot\cdot\cdot U_n < e^{-\lambda}\} - 1$$


```r
poisson <- function(lambda){
  u <- runif(1)
  N <- 1
  while(u > exp(-lambda)){
    u <- u * runif(1)
    N <- N + 1
  }
  N - 1
}

poisson(10)
#> [1] 10
mean(rdply(1000, poisson(10))$V1)
#> [1] 10.099
```

#### Ejemplo: Gamma {-}

Supongamos que deseamos generar el valor de una variable aleatoria 
$Gamma(n,\beta)$, la función de distribución es,

$$\int_{0}^x \frac{1}{\beta^n \Gamma(n)}x^{n-1}e^{-x/\beta}dy$$

* La inversa de la función de distribución acumulada anterior no se puede 
escribir de forma cerrada. 

* Sin embargo, podeos usar que una $Gamma(n,\beta)$ se puede ver como la suma 
de $n$ exponenciales independientes, cada una con parámetro $\beta$:

$$X=-\beta log(U_1)-\cdot\cdot\cdot - \beta log(U_n)$$
$$=-\beta log(U_1\cdot\cdot\cdot U_n)$$

donde la identidad $\sum log(x_i) = log(x_1\cdot\cdot\cdot x_n)$ deriva en 
ganancias computacionales.


```r
gamma_nb <- function(n, beta){
  -beta * log(Reduce(`*`,runif(10)))
}
sims_gamma <- rdply(1000, gamma_nb(n = 10, beta = 2))
mean(sims_gamma$V1)
#> [1] 19.678
var(sims_gamma$V1)
#> [1] 38.735
```

### Aceptación y rechazo {-}

* Supongamos que tenemos un método para generar variables aleatorias con función 
de densidad $g(x)$, 

* podemos usarla como base para generar observaciones de una variable aleatoria 
con densidad $f(x)$ generando $Y$ de $g$ y después aceptando 
el valor generado con una probabilidad proporcional a $f(Y)/g(Y)$. 

* Sea $c$ una constante tal que 

$$\frac{f(y)}{g(y)} \leq c$$

para toda $c$, entonces el método se puede escribir como sigue:

<div class = "caja">
**Aceptación y rechazo**

1. Genera $Y$ con densidad $g$.  
2. Genera un número aleatorio $U$.  
3. Si $U \leq \frac{f(Y)}{cg(Y)}$
define $X=Y$, de lo contrario regresa a 1.

El método de aceptación y rechazo es análogo al correspondiente a variables
aleatorias discretas.

(i) La variable aleatoria generada usando el método de aceptación y rechazo
tiene densidad $f$.  
(ii) El número de iteraciones del algoritmo que se necesitan es una variable
aleatoria geométrica con media $c$.
</div>

#### Ejemplo: Beta {-}
Usemos el método de aceptación y rechazo para generar observaciones de una 
variable aleatoria $beta(2,4)$:
$$f(x)=20x(1-x)^3$$
La variable aleatoria beta toma valores en el intervalo (0,1) por lo que consideremos $g(x)=1$, para $0<x<1$. Para determinar la menor $c$ tal que $f(x)/g(x)\leq c$ podemos derivar y obtenemos $c = 135/64$,
$$\frac{f(x)}{g(x)} \leq 20 \cdot \frac{1}{4} \bigg(\frac{3}{4}\bigg)^3 = \frac{135}{64}$$

y 

$$\frac{f(x)}{cg(x)}=\frac{256}{27}x(1-x)^3$$

por lo que el procedimiento para simular sería el siguiente:


```r
beta24 <- function(){
  # 1. Generar dos números aleatorios U_1, U_2. 
  u1 <- runif(1)
  u2 <- runif(1)
  # 2. Comparar con f(x)/cg(x)
  while(u2 > 256 / 27 * u1 * (1 - u1) ^ 3){
    u1 <- runif(1)
    u2 <- runif(1)
  }
  u1
}
sims <- rdply(1000, beta24)
mean(sims$V1)
#> [1] 0.33246
```

#### Ejemplo: Gamma(3/2, 1) {-}
Supongamos que deseamos generar simulaciones de una variable aleatoria con densidad gamma(3/2, 1):
$$f(x)=\frac{1}{\Gamma(3/2)}x^{1/2}e^{-x}$$
dado que la variable aleatoria de nuestro interés se concentra en los números positivos, y tiene media $3/2$, es conveniente usar el método de aceptación y rechazo con la variable aleatoria exponencial de la misma media.
$$g(x)=\frac{2}{3}e^{-x2/3}$$

![](imagenes/manicule2.jpg) Usa el método de aceptación y rechazo para generar 1000 observaciones de una variable aleatoria con distribución gamma(3/2,1).


#### Ejemplo: Variable aleatoria normal {-}
Nuestro objetivo es primero, simular una variable aleatoria normal estándar Z, para ello comencemos notando que el valor absoluto de Z tiene función de densidad:
$$f(x)=\frac{2}{\sqrt{2\pi}}e^{-x^2/2}$$
con soporte en los reales positivos. Generaremos observaciones de la densidad anterior usando el método de aceptación y rechazo con $g$ una densidad exponencial com media 1:

$$g(x)= e^{-x}$$

Ahora,
$\frac{f(x)}{g(x)}=\sqrt{2/\pi}e^{x - x^2/2}$
y por tanto el máximo valor de $f(x)/g(x)$ ocurre en el valor $x$ que maximiza
$x - x^2/2$, esto ocurre en $x=1$, y podemos tomar $c=\sqrt{2e/\pi}$, 
$$\frac{f(x)}{cg(x)}=exp\bigg\{x - \frac{x^2}{2}-{1}{2}\bigg\}$$
$$=exp\bigg\{\frac{(x-1)^2}{2}\bigg\}$$

 
y por tanto podemos generar el valor absoluto de una variable aleatoria con 
distribución normal estándar de la siguiente manera:

1. Genera $Y$ una variable aleatoria exponencial con tasa 1.  
2. Genera un número aleatorio $U$.  
3. Si $U \leq exp\{-(Y-1)^2/2\}$ define $X=Y$, en otro caso vuelve a 1.  

Para generar una variable aleatoria con distribución normal estándar $Z$ 
simplemente elegimos $X$ o $-X$ con igual probabilidad.

Notemos además que en paso 3 $Y$ es aceptado si $U \leq exp(-(Y-1)^2/2)$ esto es
equivalente a $-log(U) \geq (Y-1)^2/2$ y recordemos que $-log(U)$ es exponencial 
con parámetro 1, por lo que podems escribir los pasos como:

1. Genera 2 exponenciales independientes con parámetro 1: $Y_1, Y_2$.  
2. Si $Y_2 \geq (Y_1 - 1)^2/2$ define $X=Y$, de lo contrario vuelve a 1.

Supongamos ahora que aceptamos $Y_1$, esto es equivalente a decir que $Y_2$ es 
mayor a $(Y_1 - 1)^2/2$, y la diferencia $Y_2 - (Y_1 - 1)^2/2$ se distribuye 
exponencial con parámetro 1. Esto es, cuando aceptamos en el segundo paso no 
sólo obtenemos $X$ sino que calculando
$Y_2 - (Y_1 - 1)^2/2$ podemos generar una variable aleatoria exponencial con 
parámetro 1 independiente de $X$. Esto es relevante pues si estamos generando una
sucesión de variables aleatorias normales obtendríamos un algoritmo más eficiente.

