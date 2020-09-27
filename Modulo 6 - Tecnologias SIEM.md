# Modulo 6

### 1. Durante el curso, hemos visto que son los eventos correlados y los agregados ¿Podría definir brevemente cada uno de ellos? Emplee gráficos, dibujos, etc, si lo cree oportuno

Un evento agregado consiste en una sumarización de eventos que comparten distintos parámetros tales  como **IP origen,IP destino,puertos, entre otros**; los cuales  permiten llevar un conteo de los registros de forma más simplificada y reduce en gran medida la cantidad de logs que deben ser procesados por el SIEM.

**Representacion de Evento agregado**

![](/images/modulo6/agregado.PNG)

A diferencia de los eventos agregados, un evento correlado se compone de reglas para producir un evento “final”, estas reglas pueden ser de tres tipos: simples, complejas o mixtas. El origen de un evento correlado pueden ser tanto logs generados como otros eventos correlados.

**Evento Correlado**

![](/images/modulo6/correlado.PNG)

### 2.¿Se emplean siempre los mismos criteriosde agregaciónen todos los eventos o tipologíasde  eventos  que  se  reciben  en  un  SIEM? ¿Por  qué?Justifique  su respuestade acuerdo a eventos concretos o tipologíasde eventos concretas.



### 3.Durante el  curso, hemos estudiado los tipos de visualizaciones que se pueden utilizar para diferentes casuísticas(Compliance, Estado). Si quisiéramos hacer un panel en el que se desea representar gráficamente la evolución de eventos de dos fuentes de datos en un mismo periodo de tiempo, ¿Cuál sería la gráfica que utilizaría para dicho fin? Justifique su elección

En el caso de representar evolución de eventos de distintas fuentes de datos y además en un mismo periodo de tiempo, se debería usar una visualización del tipo volumetrías y distribución de eventos a lo largo del tiempo. Con un gráfico de distribución se pueden observar eventos recibidos por las distintas tecnologías o distintos dispositivos recibidos en el SIEM. En el eje vertical esta visualización tiene la suma del contador de eventos (Event Count Sum) y en el eje horizontal  tiene las horas del día donde se está receptando dichos eventos.La forma de esta gráfica es una curva suavizada que se apilan una encima de otra dependiendo de la tecnología,con esta representación se puede observar en que intervalo de tiempo se concentran más los eventos y en base a esto se realiza un posterior análisis.

### 4. Durante  el  curso, hemos  estudiado  los  tipos  de  visualizaciones  que  se  pueden utilizar  para  diferentes casuísticas  (Compliance,  Estado).  Si quisiéramoshacer una visualizaciónd elloseventos  tal  cual. ¿Cuálseríala gráficao  eltipo  de visualizaciónpara mostrar estos datos? Justifique su respuesta.

Las visualizaciones como gráficos de distribución,diagramas circulares, barras verticales, entre otros ayudan a tener una visualización ejecutiva de lo ocurrido de los logs entrantes. Sin embargo,son una forma abstracta o resumida de presentarlos,utilizando una tabla se muestra información más detallada de los logs. En  el  caso  de Arcsight en Logger se transforma el log en estado normal por una representación en tabla cuando se aplica un filtro realizando una query de la misma manera como si fuera una base de datos, el resultado de esta query origina una sumarización de los eventos con origen y contador de esos eventos.Un ejemplo de esta query puede ser agent Type=“sqlserver_audit_db”| top names



