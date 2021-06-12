# Seeder

Los seeders son una forma de poblar nuestras tablas mas rapidamente, ya sea para probar o para llenar las tablas con información básica

El codigo para crear una seeder es el sieguiente:
~~~
php artisan make:seeder CarrerasSeeder
~~~

Creara un archivo Seeder que tendra una funcion llamada  `run()`

Dentro de esta función llenaremos los datos para poblar nuestra tabla, en este caso tenemos la tabla carrerae con las columnas *id*, *clave*, *carrera*:

Llenaremos la funcion de esta forma

~~~
public function run()
{
    DB::table('carreras')->insert(['clave'=>'INNI', 'carrera'=>'Ingenieria Informatica'])
    DB::table('carreras')->insert(['clave'=>'INCO', 'carrera'=>'Ingenieria en computación'])
}
~~~

Ahora para generar el seeder en nuestra base de datos tenemos que usar el siguiente codigo
~~~
php artisan db::seed --class="nombre_de_Seeder"
~~~
en el caso de nuestro ejemplo anterior quedaría asi
~~~
php artisan db::seed --class=CarrerasSeeder
~~~
también podemos usar un modelo exportandolo al archivo `CarrerasSeeder.php`, de tal forma que al archivo agregaremos la ruta de nuestro modelo
~~~
use App\Models\Carrera;
~~~
y haciendo la insercion de esta forma
`public function run()` en `CarrerasSeeder.php`
~~~
Carrera::create(['clave'=>'CEL', 'carrera'=>'LICENCIATURA EN INFORMATICA'])
~~~
El archivo `DatabaseSeeder.php` tiene un directorio de seeder que queremos que se ejecuten en automatico, asi que para llamar a nuestro metodo anterior creado tendremos el siguiente codigo
~~~
public function run()
{
    $this->call([
        CarrerasSeeder::class
    ]);
}
~~~
Con este codigo a continuacion podemos correr las migraciones con los seeders que tengamos en `DatabaseSeeder.php`
~~~
php artisan migrate --seed
~~~

Si ejecutamos esto junto al codigo anterior, nuestra base de datos no puede validar si hay un nombre de carrera unico