# 2019-ClimateChange
Análisis del cambio climático por zonas a través del tiempo en PIG. [Kyra Cossio, Francisco Plana, María José Trujillo. Grupo 16]

# Resumen
Dada la importancia que ha cobrado en el debate público la pregunta por el cambio climático, nuestro trabajo busca mostrar el impacto observable de este fenómeno en las temperaturas registradas a lo largo de distintos lugares del planeta. Específicamente, buscamos evidenciar las tendencias de temperaturas en tierra que se pueden registrar en los últimos años, desagregadas según zonas geográficas con climas diferenciables: zonas polar norte (ZPN), templada norte (ZTN), tropical (ZTR) y templada sur (ZTS). 

# Datos
Hemos usado el dataset de Berkeley Earth (http://berkeleyearth.org/about/), una iniciativa de un grupo de científicos que busca hacerse cargo de las observaciones hechas por escépticos del cambio climático. Este dataset combina datos provenientes desde 16 archivos (http://berkeleyearth.org/source-files/), para generar series de tiempo de temperaturas mensuales, desde el año 1750 hasta el 2013, disponibles para ciudades de todo el mundo. El proceso de elaboración del dataset contempló un complejo trabajo de limpieza, agregación y corrección de sesgos, el cual fue ejecutado con código (http://berkeleyearth.org/analysis-code/) y datos (http://berkeleyearth.org/data/) que están disponibles en la web.

El dataset directamente usado en nuestro trabajo corresponde al archivo GlobalLandTemperaturesByCity.csv, una recompilación de los datos de Berkeley Earth disponible en https://www.kaggle.com/berkeleyearth/climate-change-earth-surface-temperature-data, que tiene observaciones de temperatura promedio e intervalos de confianza mensual desde 1753, disponibles según latitud y longitud (ver Gridded Data en http://berkeleyearth.org/data/).


# Métodos
Hemos decidido usar Pig, puesto que el tamaño de la base datos (8599212 filas), hace que sea un conjunto de datos manejable en memoria. Estos análisis están en zonas10.pig, en donde lo que se hace es obtener un promedio de temperaturas dentro de un año, a partir de las temperaturas mensuales de ese año, y generamos una serie de tiempo con estos promedios cada 10 años. La motivación de ocupar estos períodos de tiempo fue para reducir los datos a escalas de tiempo donde se puedan inspeccionar de forma sencilla las posibles variaciones de temperatura (o de una forma más sencilla que mirar los promedios de cada mes en períodos de 200 años). Ocupamos el promedio a lo largo de un año, para no depender de la variación estacional de temperaturas, y para dar cuenta de esta variación, calculamos además la diferencia entre temperatura máxima y mínima dentro de un año. 

Estas series de tiempo fueron generadas agregando datos en zonas según latitud. Las zonas están comprendidas por los siguientes intervalos de latitud: ZPN= (90° N , 66.01° N), ZTN= (66° N , 23.01° N), ZTR= (23° N , 23.01° S) y ZTS= (23° S , 66.01° S). El dataset no tenía datos para la zona polar sur. Por último, hicimos un análisis de completitud de datos en Analysis.pig, para encontrar conjuntos de registros de meses adyacentes, es decir, para saber si existen datos de meses faltantes en mitad de los datos. Estos análisis fueron implementados con UDF (https://pig.apache.org/docs/r0.17.0/udf.html#eval-functions) escritas en Java (código fuente disponible en cc5212_proy.jar). En general, estos análisis muestran que no hay datos de algún mes faltante en mitad del conjunto de registros asociados a una ciudad, y que desde el registro con fecha 1882-01-01, se puede asegurar que todas las ciudades poseen todos los registros mensuales hasta el año 2013.  

# Resultados

Se puede apreciar, particularmente en los promedios sobre tramos de 50 años de la serie de tiempo, que la temperatura ha subido en alrededor de 1 grado Celsius en los últimos 100-150 años. Esto se observa en todas las zonas climáticas estudiadas. Este fenómeno también se produce en las temperaturas máximas. También se aprecia un aumento de las diferencias entre máximas y mínimas. A nivel nacional, también se observan las mismas tendencias para promedios, máximos y diferencias entre máximas y mínimas. Estos resultados son consistentes con resultados publicados en 

Rohde, R., Muller, R. A., Jacobsen, R., Muller, E., Perlmutter, S., Rosenfeld, A., ... & Wickham, C. (2013). A new estimate of the average Earth surface land temperature spanning 1753 to 2011. Geoinfor Geostat Overview 1: 1. of, 7, 2.

elaborados en base a estos mismos datos.


# Conclusiones
Del trabajo realizado fue fácil calcular el promedio, máximo y mínimo dado que PIG provee nativamente funciones que permiten hacer estos cálculos. Sin embargo, al momento de querer utilizar otro tipo de estadísticas como la mediana el trabajo se complejizó, por lo que se decidió no realizar este cálculo y se deja propuesto como una mejora al análisis realizado (como un UDF por ejemplo).

Otro punto a destacar del estudio realizado es que una vez hechas las consultas a nivel global fue fácil replicarlas a nivel nacional dado que sólo cambiaban las coordenadas de las zonas y la restricción de que los datos sean sólo de Chile. Cabe señalar que, dado que tras el análisis de completitud de los datos se decidió utilizar todos los datos, esto generó ciertos casos anómalos en los resultados (un outlier en 1790 en la ZTS). Se propone a futuro generar un filtro que considere estas situaciones.

Finalmente, se concluye que haber elegido PIG para realizar los análisis fue una buena decisión ya que se lograron resultados aceptables en un tiempo moderado. Para una investigación más compleja, Pig tiene una expresividad un poco limitada dado que no provee nativamente funciones de cálculo estadístico tales como la mediana y la desviación estándar.
