# Clase 4 - CRUD (Objetos, registros)
## Formulario de tareas - STORE

Vamos a agregar en la función `create()` de nuestro controlador el retorno de una vista que crearemos en nuestra carpeta de vista de tareas como `formTarea.blade.php`
`TareaController.php`
~~~
 public function create()
    {
        return view('tareas.formTarea');
    }
~~~
Realizamos un diseño de formulario en nuestro `formTarea.blade.php` con metodo `POST`

Los nombres con los que enviamos nuestra información tienen que ser iguales que nuestras columnas

Una vez hecho el formulario tenemos que agregar nuestra ruta del formulario en web.php
~~~
Route::get('tarea/create', [TareaController::class,'create']);
~~~

Vamos a agregar el actions al formulario en el cual tenemos que dirigiar a la función store que esta en el controlador.

Para hacer esto tenemos que primero crear nuestra ruta **post** hacia store, siguiendo
~~~
Route::post('tarea', [TareaController::class,'store']);
~~~

Existe un comando, que filtra las rutas de nuestro proyecto y se le puede considerar un **directorio de rutas**
~~~
php artisan route:list | grep tarea
~~~

Vamos a comentar las rutas que tenemos, y vamos a ser mas practicos al poner nuestra ruta como `resource`; esto hará que se construyan todas las **rutas CRUD** que tenemos,
~~~
Route::resources('tarea',TareaController::class);
~~~

Para cambiar el nombre de las rutas que se llaman hay que acceder a RouteServiceProvider.php y agregar a la funcion `boot` lo siguiente :
~~~
public function boot()
{
    Route::resourceVerbs([
        'create' => 'crear',
        'edit' => 'editar',
    ]);

    // ...
}
~~~

Ahora realizado todas las rutas de nuestro CRUD, haremos nuestra funcion `store()`, nuestro proyecto siempre verifica que la información que se envía es por parte de el mismo para evitar ataques de seguridad.
**@csrf**, nos permitira generar un token para enviar información de manera segura y nos permite enviarlo a la funcion store, nuestro `tareaForm` quedaría así:
~~~
...
 <form action="/tarea" method="POST">
    @csrf
    <label for="titulo">Titulo</label>
    <input type="text" name="titulo">
...
~~~

y ahora modificaremos nuestra función `store`, creando un objeto de modelo **Tarea**, que ya trae consigo las propiedades de nuestro modelo.
### Forma 1
`public function store` en `TareaController.php`
~~~
$tarea = new Tarea();
~~~
y ahora asignaremos todos los elementos de request a nuestro objeto
~~~
    $tarea->titulo = $request->titulo;
    $tarea->descripcion = $request->descripcion;
    $tarea->prioridad = $request->prioridad;
    $tarea->tipo = $request->tipo;
    $tarea->fecha_limite = $request->fecha_limite;
    $tarea->save(); -> con esta linea guardaremos a la base de datos
~~~

y redireccionamos hacia tarea
~~~
return redirect('/tarea');
~~~
---
### Forma 2


Esta es una forma logica para crear registros, pero ahora haremos otra forma que nos ahorra la escritura de codigo:

`public function store` en `TareaController.php`
~~~
    $tarea = new Tarea($request->all());
    $tarea->save();
    return redirect('/tarea');
~~~
Nos dará un error de configuración, para lo que el framework tiene un permiso para que nos permita recibir información masiva o no. Asi que nos iremos a nuestro modelo de tarea y agregaremos lo siguiente para que nos permita la adicion masiva de datos.

`Tarea.php`
~~~
protected $fillable = ['titulo','descripcion', 'prioridad', 'fecha_limite'];
~~~
El id no se agrega, ya que ese valor no es inyectable
---
### Forma 3 - una sola linea(las propiedades del modelo tienen el mismo nombre que nuestras variables)
~~~
Tarea::store($request->all());
return redirect('/tarea');
~~~

---
## Vista de tareas - show

Este tipo de ruta busca por el id para hacer consultas , asi que en la funcion show agregaremos lo siguiente

`Tarea Controller`
~~~
    public function show(Tarea $tarea)
    {
        return view('tareas.tareaShow', compact('$tarea'));
    }
~~~

Y creamos un enlace en el *titulo* de nuestro `tareaIndex.blade.php`
~~~
        @foreach ($tareas as $tarea)
            <tr>
                <td>{{ $tarea->id }}</td>
                <td>
                    <a href="/tarea/{{ $tarea->id }}">{{ $tarea->titulo }}</a>
                    
                </td>
~~~

Esto creara que nuestros titulos se vean en enlaces y si damos click nos permitirá acceder a la vista de la tarea hecha en `tareasShow.blade.php`

En caso de que nuestra funcion show no tenga el modelo en sus parametros entonces nosotros lo tenemos que asignar
~~~
   public function show($id
    {
        $tarea = Tarea:find($id)
        return view('tareas.tareasShow', compact('tarea'));
    }
~~~

Podemos hacer el codigo mas bonito, podemos usar otro metodo para que se asigne a la ruta completa de *store* se recomienda usar estos metodos

### Forma 1

`tareaForm.blade.php`
~~~
<body>
    <h1>Formulario de tarea</h1>
    <form action="{{ action([App\Http\Controllers\TareaController::class, 'store'] )}}" method="POST"> -  Cambio en action
        @csrf
        <label for="titulo"
~~~

### Forma 2 - Mas limpia y la mejor
`tareaForm.blade.php`
~~~
<body>
    <h1>Formulario de tarea</h1>
    <form action="{{ route('tarea.store') }}" method="POST">
        @csrf
        <label for="titulo">Titulo</label>
~~~

Y ahora para cambiar el <href> de nuestros titulos podemos hacer esto, de igual forma genera la ruta completa de acceso
`tareasIndex.blade.php`
~~~
                <td>
                    <a href="{{ route('tarea.show', $tarea) }}">{{ $tarea->titulo }}</a>
                    
                </td>
~~~

También podemos cambiar la ruta a la que redirige cuando se crea nuestra tarea, aqui hay dos ejemplos

`public function store` en `TareaController.php`
~~~
    public function store(Request $request)
    {
        $tarea =  Tarea::create($request->all());
        return redirect() -> route('tarea.show', $tarea); --> Redirige a los datos de la tarea creada
    }
~~~

o

`public function store` en `TareaController.php`
~~~
return redirect()->route('tarea.index); --> Es el equivalente al menu de tareas en total
~~~