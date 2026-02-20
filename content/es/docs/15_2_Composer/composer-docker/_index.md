+++
title = 'Composer y Docker '
date = 2024-10-20T07:00:00+02:00
draft = false
icon = "fas fa-graduation-cap"
weight = 170
+++

# Cómo usar Composer cuando estamos trabajando con Docker
En desarrollo web es muy habitual trabajar con Docker.  
En proyectos PHP y Laravel necesitamos usar **Composer**, y existen varias maneras de hacerlo.

A continuación tienes las formas más sencillas, explicadas para que puedas elegir la que mejor se adapte a tu proyecto.

---

##  Opción 1: Usar Composer instalado en tu ordenador (si usas Linux o WSL)
Esta es la forma más fácil.

Instalamos Composer en el sistema:

{{< highlight bash >}}
sudo apt install composer
{{< /highlight >}}


Después podemos ejecutar Composer incluso si el proyecto está dentro de un contenedor:

{{< highlight bash >}}
composer install
composer update
{{< /highlight >}}

### Ventajas
- Muy rápido
- No hay problemas de permisos
- Funciona igual que fuera de Docker

### Inconvenientes
- Necesitas tener PHP y Composer instalados en tu máquina

---

## Opción 2: Usar Composer **dentro del contenedor**
Puedes entrar al contenedor donde corre PHP y ejecutar allí Composer:

{{< highlight bash >}}
docker exec -it web bash
composer install
{{< /highlight >}}

### Problema importante
Los archivos generados (como `vendor/`) quedan con permisos **root**, porque Composer se ejecuta dentro del contenedor.

Esto puede darte errores al editar o borrar archivos.

---

## Opción 3: Instalar Composer dentro del Dockerfile (la mejor para producción)
Es la opción profesional.  
No descargamos nada a mano: **copiamos Composer directamente desde la imagen oficial**.

Ejemplo de Dockerfile:

{{< highlight dockerfile >}}
FROM php:8.2-apache

# Copiar Composer desde la imagen oficial
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
{{< /highlight >}}

### Ventajas
- Es 100% reproducible (lo mismo para todos los desarrolladores)
- Más seguro y más limpio que usar curl
- Composer siempre disponible en el contenedor

### Inconvenientes
- En desarrollo local, los vendors serán creados por root (igual que en opción 2)

---

## Opción 4: Alias “composer-docker” (no instalas nada en tu PC)
Podemos crear un alias que ejecuta Composer
Para ello necesitamos crear una función, ya que necesitamos parámetros, y esto no lo podemos hacer con un alias

{{< highlight bash >}}
composer-docker () {
 docker exec -it web bash -c "cd /var/www/html/$1 && composer ${@:2}"
}
{{< /highlight >}}
* $1 => es el primer parámetro (el proyecto dónde queremos ejectuar composer)
* ${@:2} => Resto de parámetros desde el segundo  

Opcionalmente si siempre vamos a ejecutar composer en la carpeta correspondiente (recomendado), mejor usar

{{< highlight bash >}}
composer-docker () {
PROJECT=$(basename "$PWD")
docker exec -it web bash -c "cd /var/www/html/$PROJECT && composer $@"
}
{{</highlight>}}
* En este caso pwd es el directorio actual
* $@ representa todos los parámetros
Uso:

{{< highlight bash >}}
composer-docker install
composer-docker update
{{< /highlight >}}

### Ventaja
- No instalas Composer en tu ordenador

### Inconveniente
- Los archivos generados serán de root

---

#  ¿Cuál deberías usar ?
- **Linux o WSL → Opción 1 (Composer en tu sistema).**
- **Windows sin WSL → Opción 4.**
- **Proyectos profesionales → Opción 3 (Dockerfile con COPY).**
- **Si ya estás dentro del contenedor → Opción 2.**

---

