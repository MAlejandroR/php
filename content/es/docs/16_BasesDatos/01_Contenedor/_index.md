+++
title = 'Contenedor '
date = 2024-10-15T07:04:49+02:00
draft = false
icon = "fa fa-database"
weight = 10
+++

## Confituración con nuestro contenedor
Este es hasta ahora nuestro contenedor para mysql y vamos a realizar alguna modificación
{{< highlight php tabla_alumnos "linenos=table, hl_lines=" >}}
mysql:
image: mysql
volumes:
- mysql:/var/lib/mysql
environment:
- MYSQL_ROOT_PASSWORD=root12345
- MYSQL_USER=alumno
- MYSQL_PASSWORD=alumno
- MYSQL_DATABASE=tienda
container_name: mysql
{{< /highlight>}}

### Cómo cargar una base de datos ya construída
Cuando MySQL arranca por primera vez, el contenedor ejecuta automáticamente todos los archivos .sql que existan dentro del directorio:

/docker-entrypoint-initdb.d/

Con esto conseguimos las siguientes acciones:
* Copiamos datos.sql desde tu proyecto al contenedor.

* El contenedor MySQL lo detecta gracias a su entrypoint oficial.

* MySQL crea la base de datos y ejecuta el contenido del SQL automáticamente.

* Esto solo ocurre la primera vez, es decir, cuando el directorio de datos (/var/lib/mysql) está vacío. Si lo quiero volver a cargar, tendría que borrar la base de datos y volver a lanzar el contenedor
```bash
volumes:

- ./datos.sql:/docker-entrypoint-initdb.d/datos.sql
```

### Estableciendo un sistema de codificación
Para ello vamos a establecer un fichero de configuración que va a tener 3 secciones:
{{< highlight php tabla_alumnos "linenos=table, hl_lines=" >}}
[client]


[mysql]

[mysqld]
{{< /highlight>}}
Vemos cada sección muy breve.
{{<desplegable title="cliente">}}

Configura el **charset por defecto de los clientes** que se conectan a MySQL.

Afecta a:
- phpMyAdmin
- comandos externos
- herramientas de administración

Garantiza que todo cliente use UTF8MB4 al conectarse.

---
{{</desplegable>}}
{{<desplegable title="mysql (CLI)">}}

Afecta **solo al cliente de consola** `mysql`.

Ejemplo: cuando entras a MySQL así:
```bash
phpmysql -u root -p

```


Este bloque hace que **la propia consola use utf8mb4**, evitando errores al escribir acentos o emojis.
{{</desplegable>}}

{{<desplegable title="mysqld (El servidor, EL IMPORTANTE)">}}


Este es **el servidor MySQL real**.

Aquí defines:
- `character-set-server=utf8mb4`  
  → El charset por defecto de TODAS las bases de datos nuevas.

- `collation-server=utf8mb4_unicode_ci`  
  → Reglas de comparación y ordenación basadas en Unicode.

- `skip-character-set-client-handshake`  
  → Fuerza utf8mb4 aunque un cliente solicite otro charset  
  (por ejemplo, herramientas antiguas).

Esto asegura que **PHP, Laravel y phpMyAdmin siempre trabajen en UTF8MB4**.
{{</desplegable>}}
---
### ¿Por qué usar UTF8MB4?

`utf8mb4` es la versión correcta de UTF-8 en MySQL.

El antiguo `utf8` está limitado y no soporta todos los caracteres.

### Ejemplo

| Carácter | ¿utf8 lo soporta? | ¿utf8mb4 lo soporta? |
|---------|---------------------|-----------------------|
| á       | ✔ sí                | ✔ sí                 |
| ñ       | ✔ sí                | ✔ sí                 |
| 😊      | ❌ NO               | ✔ sí                 |
| 𐍈      | ❌ NO               | ✔ sí                 |

 **Conclusión:**  
**Siempre usa {{<color>}}utf8mb4{{</color>}}. El antiguo utf8 de MySQL está roto y no debe usarse.**

---

## ¿Qué es la collation?

La *collation* define:

- cómo se compara texto
- cómo se ordena
- si distingue mayúsculas
- si distingue acentos

Es como el diccionario de MySQL.

---

## ¿Qué significa `unicode_ci`?

### ✔ `unicode`
Usa reglas de Unicode internacional → funciona bien para todos los idiomas.

Ejemplo: ordena bien á, é, í, ó, ú, ñ.

### ✔ `_ci` → *Case Insensitive*
No diferencia entre:

- a y A
- á y Á
- e y É

Por eso esta collation es ideal para aplicaciones web.



{{< highlight php tabla_alumnos "linenos=table, hl_lines=" >}}
volumes:
- ./my.cnf:/etc/mysql/conf.d/my.cnf
{{< /highlight>}}
* Entonces el fichero de configuración 
{{< highlight php tabla_alumnos "linenos=table, hl_lines=" >}}
[client]
default-character-set=utf8mb4

[mysql]
default-character-set=utf8mb4

[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
skip-character-set-client-handshake
{{< /highlight>}}


<div class="iframe-container">
<iframe src="https://es.wikieducator.org/index.php?curid=6709" width="100%" height="844">WikiEducator </iframe>
</div>








