# Database Laravel - Migraciones
-Laravel Breeze

-Laravel Fortify (Cookies)

-Laravel Jetstream (Provee autentificación normal web, como accesos dados por Fortify)

## Laravel Jetstream
Para evitar cualquier problema en la instalación hay que irnos al archivo de php.ini y dejar la `memory_limit` en `-1`

Ejecutaremos este codigo para la instalación

~~~
composer require laravel/jetstream
~~~

Después instalamos Livewire con 
~~~
php artisan jetstream:install livewire --teams
~~~

Neceistamos actualizar npm y node

~~~
npm install npm
npm install node
npm install
~~~

Este archivo nos genera nuestro archivos de CSS y de JavaScript por default en public 
~~~
npm run dev
~~~

Una vez hecho las instalaciones, si abrimos nuestro proyecto abra un error en el cual se muestra que no hemos creado nuestra base de datos ya que no se ha creado.
Tenemos que crear la base de datos y agregar su nombre a `.env`

Seguirá dando un error de sesiones, por lo que ejecutremos
~~~
php artisan migrate
~~~ 

Las migraciones son archivos que van a tener el diseño de nuestra base de datos en codigo, lo que hace este comando es que se actualiza nuesta base de datos.
Se agregará una sección de login y register en nuestra página, y a nuestra base de datos se le agregarán las tablas necesarias de nuestra migración.

Si vamos a la carpeta de `DATABASE/MIGRATIONS`, tenemos como se crean esas tablas

La ventaja de esto es que si se quiere agregar algo, se actualiza desde el codigo para trabajar en cualquier lado.

## Instalación en blanco

Para instalar con Jetstream entonces tenemos que ejecutar esto

~~~ 
laravel new tareas --jet
~~~ 

Crea una aplicación nueva con todos los paquetes que se requieran.