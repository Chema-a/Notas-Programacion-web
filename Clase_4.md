# Clase 4 - CRUD (Migraciones, Controladores)

## Migraciones

Creamos migración para generar la tabla

~~~
php artisan make:migration create_tareas_table --create
~~~ 
Crea dos metodos en la clase

-up() -> Empuja 

-down() -> Revierte/Deshace 

Nos crea un archivo de migración para la tabla de tareas en la carpeta de `database` con las siguiente funcion
~~~
  public function up()
    {
        Schema::create('tareas', function (Blueprint $table) {
            $table->id(); -- Genera columna de id autoincrementable
            $table->timestamps();  --Se actualiza conforme se hacen los archivos
        });
    }
 ~~~

 Agregaremos para que genere las columnas que necesitemos
~~~
    public function up()
    {
        Schema::create('tareas', function (Blueprint $table) {
            $table->id();
            $table->string('titulo');
            $table->text('descripcion');
            $table->smallInteger('prioridad')->default(1);
            $table->date('fecha_limite');
            $table->timestamps();
        });
    }
~~~

Una vez creado el archivo de creación de tabla ejecutamos lo siguiente para que se genera nuestra tabla
~~~
php artisan migrate
~~~

Si queremos regreasr al punto anterior donde no este la tabla tareas podemos usar el comando
~~~
php artisan migrate:rollback
~~~

## Controlador

Vamos a hacer un controlador para usar nuestra base de datos
~~~
php artisan make:controller TareaController
~~~
Una vez creado el controlador queremos tener los siguientes metodos básicos de consulta:

**CRUD**
- Listado -> index
- Form Creacion -> create
- Guardar a DB -> store
- Ver detalle -> show
- Form Editar -> edit
- Guardar cambios a DB -> update
- Elimar -> delete

Todos estos metodos podemos crearlos con
~~~
php artisan make:controller TareaController -r
~~~

Organizamos nuestras vistas al crear una carpeta solo para tareas y creamos el archivo `tareaIndex.blade.php` dentro de ella

Vamos a implementar un index ahora en TareasController
`public function index()`

~~~
{
    return view('tareas.tareasIndex.blade.php');
}
~~~
Y ahora creamos nuestra ruta de tareas en  `web.php`
~~~
Route::get('tarea', [TareaController::class,'index']);
~~~

Vamos a mostar los datos de nuestra tabla tareas

## Forma 1 - No hay que hacer esto 
Agreamos a nuestro `TareaController` el namespace :

~~~
use Illuminate\Support\Facades\DB;
~~~

y nuestra función index, quedaria asi para pasar los datos de la tabla 
~~~
$tareas = DB::table('tareas')->get();
return view('tareas/tareasIndex', compact('tareas'));
~~~

y en nuestro `tareasIndex.blade.php` haremos un ciclo para mostrar todo 

~~~
        @@foreach ($tareas as $tarea)
            <tr>
                <td>{{ $tarea->id }}</td>
                <td>{{ $tarea->titulo }}</td>
                <td>{{ $tarea->descripcion }}</td>
                <td>{{ $tarea->fecha_limite }}</td>
            </tr>
        @endforeach
~~~

## Forma 2 - Creación de Modelo - OR
M

Creamos un modelo con 

~~~
php artisan make:model Tarea
~~~

Laravel asume que existe una tabla tareas al pluralizar el nombre de nuestro modelo por lo que siempre tiene que estar en **singular**

Ahora modificaremos nuestro `TareaController` para usar el modelo:

Agregamos el namespace de nuestro modelo
~~~
use App\Models\Tarea; 
~~~

Y en nuestra funcion index()
~~~
$tareas = Tarea::all();
o
$tareas = Tarea::get();
~~~

o podemos manejar otros paramteros

~~~
$tareas = Tarea::where('id', '=', 1)->get();
~~~

Documentacion Query Builder : https://laravel.com/docs/8.x/queries

En caso de que se necesite especificar el nombre de la tabla en nuestro modelo tenemos que agreagar esto en el codigo de nuestro modelo


~~~
protected $table = 'nombre_de_tabla'
~~~
---
Podemos crear un controlador con un modelo de Tarea ya asignado de la siguiente forma, nos ahorra la importacion del modelo y nos agrega los modelos a nuestros metodos:

~~~
php artisan make:controller TareaController -r --model=Tarea
~~~