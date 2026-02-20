
+++
title = 'Bases de datos en php'
date = 2024-10-15T07:04:49+02:00
draft = false
icon = "fa fa-database"
weight = 160
+++


### Qué veremos aquí
En este tema se abordan 5 aspectos

* {{<color>}}Los 3 primeros serían repaso.{{</color>}}

* {{<color>}}Los 2 últimos, son temas específicos para trabajar con bases de datos desde ''''php'''''.{{</color>}}
<hr />

#### Temas de repaso:
{{<color>}}Qué es una base de datos{{</color>}}

* Son conceptos ya conocidos
* Simplemente para establecer unos términos comunes para todos
* Poner en común ideas ya sabidas y aprendidas en primero

{{<color>}}SQL{{</color>}}

*Lenguaje de 4 generación no procedural
* A pesar de su nombre (Lenguaje de consultas), no es sólo un lenguaje de consultas;Es un lenguaje completo para gestionar bases de datos
* Incorpora instruciones para adminstrar(DCL), construir (DDL) y manipular (DML) la base de datos.
* Sus acciones o sentencias empiezan por una cláusula que la clasifica en el tipo de lenguaje o acción
  1. {{<color>}}DDL{{</color>}}
  {{< highlight sql >}}
  CREATE ....
  DROP ....
  ALTER ....
  {{</highlight>}}
2. {{<color>}}DML{{</color>}}
  {{< highlight sql >}}
  INSERT ....
  UPDATE ....
  DELETE ....
  SELECT ...
  {{</highlight>}}
3. {{<color>}}DCL{{</color>}}
  {{< highlight sql >}}
  GRANT ....
  REVOKE ....
  COMMIT
  ROLLBACK
  {{</highlight>}}
  
  {{<color>}}MYSQL{{</color>}}
  * Un gestor de bases de datos relacional
  * Conviene saber usarlo 
  * En línea de comandos
  * Con una herramienta gráfica como phpmyadmin

### Temas de específicos : Drivers o conectores para la base de datos desde php
* Es la parte específica del módulo
* Son recursos que ya incorpora el motor de php para interactuar desde nuestro programa con la base de datos
* En ellos debemos aprender a 
> 1. conectarnos
> 2. realizar acciones DML
> 3.  tratar la información que nos devuelva una consulta

{{<color>}}mysqli{{</color>}}
* Recurso nativo escrito en php para conectar con una base de datos ubicada en un gestor '''''mysqli''''' o '''''maria'''''

{{<color>}}PDO{{</color>}}
* Recurso de php que nos permitirá conectarnos con cualquier gestor de bases de datos relacional
