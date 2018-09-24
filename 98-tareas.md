# Tareas {-}

* Las tareas se envían por correo a teresa.ortiz.mancera@gmail.com con título: 
EstComp-TareaXX (donde XX corresponde al número de tarea, 01..). 

* Las tareas deben incluir código y resultados (si conocen [Rmarkdown](https://rmarkdown.rstudio.com) 
es muy conveniente para este propósito).


## 2-Transformación de datos {-}

Entrega: Lunes 27 de agosto.

Utiliza los datos de vuelos (`flights`) para responder la siguientes preguntas.

* ¿A qué hora del día debo volar para evitar, lo más posible, retrasos de 
salida?

* Para cada destino calcula el total de minutos de retraso de salida. Para cada 
vuelo calcula su proporción del total de retrasos de su destino.

* Los retrasos suelen estar correlacionados temporalmente, incluso cuando se 
ha resuelto el problema que ocasionó los retrasos iniciales, vuelos posteriores
suelen mantener algo de retraso. Usando la función `lag()` explora como el 
retraso de salida de un vuelo se relaciona con el retraso de salida del vuelo 
anterior. Realiza una gráfica para visualizar tus hallazgos.

* Para cada destino, puedes encontrar vuelos sospechosamente rápidos o lentos?
(quizá debido a porblemas en la captura de los datos). Calcula el tiempo de 
vuelo relativo a la mediana de tiempo de vuelo a su destino. Qué vuelos se 
retrasaron más en el aire?

* Encuentra los destinos que se vuelan por al menos dos compañías (carriers).

## 3-Datos Limpios {-}

Entrega: Lunes 3 de septiembre.

Descarga los datos [aquí](https://www.dropbox.com/s/e8wjbwpwa37ceqg/03-limpios.zip?dl=0).

En la carpeta de arriba encontrarás un archivo de excel (m_013.xls), este 
archivo contiene información de causas de mortalidad en México entre 2000 y 
2008. Contesta las siguientes preguntas:

1. ¿Cuáles son las variables en esta base de datos?  
2. ¿La tabla de datos cumple con los principios de datos limpios? 
¿Qué problemas presenta?  
3. La información del archivo de excel se ha guardado también en archivos de 
texto (csv) 2001-2008, lee y limpia los datos para que cumplan los principios de 
datos limpios. Recuerda que las modificaciones deben de ser reproducibles, para 
esto guarda tu trabajo en un script.  
4. El archivo de excel indice_marginacion.xlsx contiene el índice por entidad 
para los años 2000 y 2010. Realiza una gráfica donde compares la marginación por 
entidad con las tasas de mortalidad correspondientes al 2000. Deberás unir las 
dos fuentes de información.

Observaciones:  

* Puedes filtrar/eliminar los valores a *Total* si crees que es más claro. 

* Intenta usar las funciones que estudiamos en la clase (gather, separate, 
select, filter).  

* Si aún no te sientes cómodx con las funciones de clase (y lo intentaste varias 
veces) puedes hacer las manipulaciones usando otra herramienta (incluso Excel, 
una combinación de Excel y R o cualquier software que conozcas); sin embargo, 
debes documentar tus pasos claramente, con la intención de mantener métodos 
reproducibles.

## 4-Probabilidad {-}

* Urna: 10 personas (con nombres distintos) escriben sus nombres y los ponen en 
una urna, después seleccionan un nombre (al azar). 

    - Sea A el evento en el que ninguna persona selecciona su nombre, ¿Cuál es 
    la probabilidad del evento A?  

    - Supongamos que hay 3 personas con el mismo nombre, ¿Cómo calcularías la 
    probabilidad del evento A en este nuevo experimento?

* Definimos $X$ como la variable aleatoria del 
número de juegos antes de que termine el experimento de la ruina del jugador, 
grafica la distribución de probabilidad de $X$ 
(calcula $P(X=1), P(X=2),...,P(X=100)$).

## 5-Bootstrap{-}

1. **Distribución muestral.** Consideramos la base de datos [primaria](https://raw.githubusercontent.com/tereom/est-computacional-2018/master/data/primarias.csv), 
y la columna de calificaciones de español 3^o^ de primaria (`esp_3`). 

- Seleccina una muestra de tamaño $n = 10, 100, 1000$. Para cada muestra 
calcula media y el error estándar de la media usando el principio del *plug-in*:
$\hat{\mu}=\bar{x}$, y $\hat{se}(\bar{x})=\hat{\sigma}_{P_n}/\sqrt{n}$.

- Ahora aproximareos la distribución muestral, para cada tamaño de muestra $n$: 
i) simula 10,000 muestras aleatorias, ii) calcula la media en cada muestra, iii)
Realiza un histograma de la distribución muestral de las medias (las medias del
paso anterior) iv) aproxima el error estándar calculando la desviación estándar
de las medias del paso ii.

- Calcula el error estándar de la media para cada tamaño de muestra usando la
información poblacional (ésta no es una aproximación), usa la fórmula:
$se_P(\bar{x}) = \sigma_P/ \sqrt{n}$.

- ¿Cómo se comparan los errores estándar correspondientes a los distintos 
tamaños de muestra? 

2. **Bootstrap correlación.** Nuevamente trabaja con los datos `primaria`, 
selecciona una muestra aleatoria de tamaño 100 y utiliza el principio del 
_plug-in_ para estimar la correlación entre la calificación de $y=$español 3 y 
la de $z=$español 6: $\hat{corr}(y,z)=0.9$. Usa bootstrap para calcular el error
estándar de la estimación.