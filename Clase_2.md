# **Clase 2  - Laravel**
## **Vistas, rutas y controladoras**

Creamos una ruta dentro de **/routes/web.php** creando una función anonima que se llama y se ejecuta

Esto al llamarlo en **proyecto-d13.test/bienvenida** , nos dará el mensaje de **"Bienvenida"**
~~~
Route::get('/bienvenida', function() {
return 'Bienvenida;'
});
~~~

Ahora para crear una vista, llamará a un archivo que tiene nuestra "vista"

~~~
Route::get('/bienvenida', function() {
return view('bienvenida');
});
~~~
Creamos una vista dentro de **/resources/views** llamado **bienvenida.blade.php.** con estructura de html5.

> Nota: Las vistas van en minisuculas y las clases en mayusculas

Todas las vistas tienen que tener terminación **.blade.php** ya que es un motor de preplantillas que ejecuta el codigo php de forma más limpia

Para las vistas escenciales podemos agruparlas en una carpeta para facilitar su acceso en este caso se creo la carpeta con la siguiente dirección **/resources/views/inicio**

Hecho esto tendremos que cambiar nuestra ruta:
~~~
Route::get('/bienvenida', function() {
return view('inicio/bienvenida');
~~~

                        o
~~~
return view('inicio.bienvenida');
});
~~~

Para recibir parametros dentro de la ruta, hay que modificar la ruta, en el que espera un parametro y lo asociará a una variable llamada **"nombre"** que pasaremos a nuestra funcion y entrará al bloque de codigo de view

~~~
Route::get('/bienvenida/{nombre}', function($nombre) {
dd($nombre); Nos permite ver la variable y termina la funcion
return view('inicio/bienvenida');`
});
~~~

Para pasar la variable a nuestra función tenemos que utilizar el metodo `compact()` y dentro la variable
~~~
Route::get('/bienvenida/{nombre}', function($nombre) {
return view('inicio/bienvenida', compact('nombre'));
});
~~~
Modificamos nuestro archivo **bienvenida** para mostar la variable
~~~
<body>
<h1>Hola {{ $nombre }}</h1>
</body>
~~~

Nuestra ruta a fuerzas espera un parámetro, por lo que para cuando queremos que un parámetro sea opcionales le agregamos un `?`, y se le puede asignar un nombre a la variable dentro de la función como se especifica
~~~
Route::get('/bienvenida/{nombre?}', function($nombre = 'Victor')
~~~

Otra forma de pasar información es pasarle un arreglo con el metodo `->with();`

~~~
Route::get('/bienvenida/{nombre}/{apellido}', function($nombre = "Victor", $apellido = "Gonzales") {
$nombreMayusculas = strtoupper($nombre);
return view('inicio/bienvenida', compact('nombre'))->with(['nombreM' => $nombreMayusculas]);
});
~~~

Y modificamos la vista para que muestre nombreM
~~~
<p>{{ $nombreM }}</p>
~~~
Creamos otra ruta para la vista contacto y creamos su archivo .blade.php **/resources/views/inicio/contacto.blade.php**
~~~
Route::get('/contacto', function() {
return view('inicio.contacto');
});
~~~
En este tipo de casos que nuestras vistas tienen elementos similar vamos a utilizar un **layout**, por lo que se recomienda crear una carpeta llamada layouts dentro de **views**.
Crearemos un archivo llamado basico.blade.php para que tenga los elementos basicos de html5. Y lo unico que cambiara es el body por lo que se tiene que indicar donde se inyectara información, por lo que nuestro archivo de layout basico quedara como:
~~~
<body>
@yield('contenido')  
</body>
~~~
Para los archivos que hagan uso del layout tendrán que tener el siguiente codigo:  
~~~
@extends('layouts/basico')
@section('contenido')
   
##Contenido##

 @endsection
~~~
Y nuestro archivo bienvenida.blade.php cambiara para que se inyecte la información de la siguiente forma:

~~~
@extends('layouts/basico')

@section('contenido')
    <h1>Hola {{ $nombre }}</h1>

    <p>{{ $nombreM }}</p>

@endsection
~~~
Esto nos ayuda a minimizar el tamaño y trabajo del código

## Controladores

Para mejorar y trabajar correctamente la funcionalidad vamos a extraer las operaciones y tareas a un controlador

Para crearlo de forma rapida, nos vamos a nuestra terminal y dentro de la carpeta de nuestro proyecto utilizaremos : 
~~~
php artisan make:controller InicioController
~~~
>Nota: todas las clases van en mayuscula y se puede agregar Controller para indicar que es un controlador

Y nos crea nuestro controlador dentro de la carpeta **app/http/Controllers/**

Ahora lo que haremos es extraer nuestras funciones en la ruta para que las trabaje nuestro controlador, por lo que __InicioController__ quedaría asi :

~~~
>`<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;

class InicioController extends Controller
    {
    public function bienvenida($nombre = 'Victor')
        {
        $nombreMayusculas = strtoupper($nombre);
        return view('inicio/bienvenida', compact('nombre'))->with(['nombreM' => $nombreMayusculas]);
        }
}
~~~

Y referiremos a nuestro controlador en la ruta que se requiere, quedando asi : 
~~~
Route::get('/bienvenida/{nombre?}', [InicioController::class, 'bienvenida']);
~~~

Y se tiene que agregar el namespace de **Inicio Controller** al archivo de **web.php**, donde estan nuestras rutas

~~~
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\InicioController;
~~~

Hacemos lo mismo con la ruta de contacto, modificando nuestro ruteo y agregando la función en InicioController

**Ruta - web.php**
~~~
Route::get('/contacto', [InicioController::class, 'contacto']);
~~~
**Controlador- InicioController.php**
~~~
 ...
    public function contacto()
    {
      return view('inicio.contacto');
    }
~~~
