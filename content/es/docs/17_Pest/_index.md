+++
title = 'Prueba unitaria'
date = 2024-10-15T07:04:49+02:00
draft = true
icon = "fa fa-database"
weight = 180
+++


### Objetivos de este tema
{{< objetivos  >}}
Repaso de bases de datos
Gestor de bases de datos Mysql
Conectores o Recursos en php para acceder a datos
Estudo de mysqli
Estudo de PDO
{{< /objetivos >}}

# Introducción a las Pruebas Unitarias con Pest en PHP

Las pruebas unitarias permiten verificar que cada parte de nuestro código funciona correctamente de manera aislada. En PHP, podemos usar herramientas como PHPUnit o Pest para escribir y ejecutar pruebas automatizadas.

## ¿Qué es Pest?
{{<definicion title="Pest" icon="code">}}
Pest es un marco de pruebas para PHP que proporciona una sintaxis más simple y expresiva en comparación con PHPUnit. Está diseñado para escribir pruebas unitarias de forma más rápida y clara.
{{</definicion>}}

{{% line %}}

## Instalación de Pest

Para instalar Pest en un proyecto PHP, usa Composer:

{{< highlight dockerfile "linenos=table, hl_lines=1" >}}
composer require pestphp/pest --dev
vendor/bin/pest --init
{{< /highlight >}}

Esto instalará Pest y creará la estructura necesaria para escribir pruebas.

{{% line %}}

## Escribiendo la Primera Prueba

Supongamos que tenemos la siguiente clase:

{{< highlight dockerfile "linenos=table, hl_lines=1" >}}
<?php

namespace App;

class Fecha
{
    private int $year;

    public function __construct(int $year)
    {
        $this->year = $year;
        $this->valida_fecha();
    }

    public function getYear(): int
    {
        return $this->year;
    }

    public function setYear(int $year): void
    {
        $this->year = $year;
    }

    private function valida_fecha(): void
    {
        if ($this->year < 1900 || $this->year > 2100) {
            throw new \InvalidArgumentException("El año debe estar entre 1900 y 2100.");
        }
    }
}
{{< /highlight >}}

Para probarla con Pest, creamos un archivo en `tests/FechaTest.php`:

{{< highlight dockerfile "linenos=table, hl_lines=1" >}}
<?php

use App\Fecha;

test('se puede obtener y establecer el año', function () {
    $fecha = new Fecha(2024);
    expect($fecha->getYear())->toBe(2024);

    $fecha->setYear(2030);
    expect($fecha->getYear())->toBe(2030);
});

test('lanza excepción si el año no es válido', function () {
    expect(fn () => new Fecha(1800))->toThrow(\InvalidArgumentException::class);
    expect(fn () => new Fecha(2200))->toThrow(\InvalidArgumentException::class);
});
{{< /highlight >}}

{{% line %}}

## Explicación de las Pruebas

{{< alert title="Importante" color="blue" >}}
Las pruebas con Pest utilizan una sintaxis más clara que PHPUnit, permitiendo escribir `expect(...)->toBe(...)` en lugar de métodos como `assertEquals()`.
{{< /alert >}}

- **Primera prueba** (`se puede obtener y establecer el año`):
  - Se crea un objeto `Fecha` con el año `2024` y se verifica que `getYear()` devuelve `2024`.
  - Se cambia el año a `2030` con `setYear()` y se verifica el nuevo valor.

- **Segunda prueba** (`lanza excepción si el año no es válido`):
  - Se intenta crear un objeto `Fecha` con `1800` y `2200`, y se espera que Pest lance una excepción `InvalidArgumentException`.

{{% line %}}

## Ejecutando las Pruebas

Para ejecutar las pruebas, simplemente usa:

{{< highlight dockerfile "linenos=table, hl_lines=1" >}}
vendor/bin/pest
{{< /highlight >}}

Si todo está bien, verás un mensaje indicando que las pruebas han pasado ✅.

{{% line %}}

## Beneficios de Usar Pest

{{<desplegable title="Ventajas de Pest">}}
- **Sintaxis simple**: Usa una sintaxis clara y sin clases de test extensas.
- **Integración con PHPUnit**: Se puede usar junto con PHPUnit sin problemas.
- **Mayor legibilidad**: Los tests son más fáciles de escribir y entender.
{{</desplegable>}}

{{% line %}}

# 📌 Cheat Sheet de PestPHP

Pest ofrece una sintaxis clara y expresiva para escribir pruebas en PHP. Aquí tienes un resumen de los métodos más usados con `expect()` y otros objetos útiles.

{{% line %}}

## ✅ `expect()` y sus Métodos

`expect()` es la base de las pruebas en Pest y permite hacer aserciones sobre valores.

{{< highlight dockerfile "linenos=table, hl_lines=1" >}}
expect(5)->toBe(5); // ✅ Pasa la prueba
expect(5)->not->toBe(10); // ✅ Pasa la prueba
{{< /highlight >}}

### Métodos Disponibles

| Método                 | Descripción |
|------------------------|-------------|
| `toBe($valor)`        | Verifica que el valor sea exactamente igual (`===`). |
| `not->toBe($valor)`   | Verifica que el valor **no** sea igual. |
| `toEqual($valor)`     | Verifica que el valor sea igual (`==`). |
| `toBeNull()`          | Verifica que el valor sea `null`. |
| `toBeTrue()`          | Verifica que el valor sea `true`. |
| `toBeFalse()`         | Verifica que el valor sea `false`. |
| `toBeGreaterThan($n)` | Verifica que sea mayor que `$n`. |
| `toBeLessThan($n)`    | Verifica que sea menor que `$n`. |
| `toContain($item)`    | Verifica que un array o string contenga el valor `$item`. |
| `toHaveCount($n)`     | Verifica que un array tenga exactamente `$n` elementos. |
| `toBeInstanceOf($clase)` | Verifica que un objeto sea instancia de `$clase`. |
| `toThrow($excepcion)` | Verifica que se lance una excepción específica. |

{{% line %}}

## 🔥 Expectativas para Arrays y Cadenas

| Método                 | Descripción |
|------------------------|-------------|
| `toContain($valor)`   | Verifica si un array o string contiene el `$valor`. |
| `toMatch($regex)`     | Verifica si una cadena coincide con una expresión regular. |
| `toStartWith($str)`   | Verifica si una cadena empieza con `$str`. |
| `toEndWith($str)`     | Verifica si una cadena termina con `$str`. |

Ejemplo:

{{< highlight dockerfile "linenos=table, hl_lines=1" >}}
expect(['PHP', 'Laravel', 'Pest'])->toContain('Pest'); // ✅
expect('PestPHP')->toStartWith('Pest'); // ✅
{{< /highlight >}}

{{% line %}}

## 🛠 `test()` y Agrupación de Pruebas

Pest permite agrupar pruebas de forma sencilla:

{{< highlight dockerfile "linenos=table, hl_lines=1" >}}
test('sumar dos números', function () {
    $calc = new Calculadora();
    expect($calc->sumar(2, 3))->toBe(5);
});
{{< /highlight >}}

Para agrupar varias pruebas relacionadas:

{{< highlight dockerfile "linenos=table, hl_lines=1" >}}
describe('Pruebas de la Calculadora', function () {
    test('sumar dos números', function () {
        $calc = new Calculadora();
        expect($calc->sumar(2, 3))->toBe(5);
    });

    test('restar dos números', function () {
        $calc = new Calculadora();
        expect($calc->restar(5, 2))->toBe(3);
    });
});
{{< /highlight >}}

{{% line %}}

## ⚠️ Excepciones y Errores

Puedes probar excepciones fácilmente con `toThrow()`:

{{< highlight dockerfile "linenos=table, hl_lines=1" >}}
expect(fn () => new Fecha(1800))->toThrow(\InvalidArgumentException::class);
{{< /highlight >}}

Si necesitas verificar un mensaje específico de la excepción:

{{< highlight dockerfile "linenos=table, hl_lines=1" >}}
expect(fn () => new Fecha(1800))
    ->toThrow(\InvalidArgumentException::class, "El año debe estar entre 1900 y 2100.");
{{< /highlight >}}

{{% line %}}

## 🏗 Hooks: `beforeEach()` y `afterEach()`

Pest permite ejecutar código antes o después de cada prueba.

```dockerfile
beforeEach(function () {
    $this->calc = new Calculadora();
});

test('sumar dos números', function () {
    expect($this->calc->sumar(2, 3))->toBe(5);
});

## Referencias
{{<referencias title="Documentación de Pest" subtitle="Guía oficial y ejemplos" icon-image="pest_logo.png">}}
- [PestPHP - Documentación Oficial](https://pestphp.com/)
- [Pruebas en Laravel con Pest](https://laravel.com/docs/10.x/testing#pest)
{{</referencias>}}

