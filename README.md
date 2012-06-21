#Slug Behavior.
###Enables a model object to sluggable behavior.

# Comportamiento para URLs Amigables.

En ocasiones queremos que nuestros _registros_ sean accedidos por otro campo clave ademas del ID, por ejemplo en el sitio web tenemos **artículos** los cuales tiene los siguientes campos:

 - id - Identificador (campo auto incrementable)
 - nombre - Es campo es único también.
 - contenido - texto el cual podría ser vació.


Ahora según el estándar del cakephp nos dice que para acceder al primer, articulo el cual se llama "algún articulo" tendríamos las siguientes **URLs**:


	#visualizando un articulo
	http://mi-sitio/articulos/ver/1

	#Editando un articulo
	http://mi-sitio/articulos/editar/1
	
	#Borrando un articulo
	http://mi-sitio/articulos/borrar/1

 
Imaginemos que queremos hacer que nuestros artículos se pudieran acceder a partir de las siguientes **URLs**:
	
	#visualizando un articulo
	http://mi-sitio/articulos/ver/algun-articulo.html
	
	#Editando un articulo
	http://mi-sitio/articulos/editar/algun-articulo.html
	
	#Borrando un articulo
	http://mi-sitio/articulos/borrar/algun-articulo.html


Precisamente este `Slug Behavior` nos permite hacer esto con cakephp, te interesa continua leyendo...


# Instrucciones de uso.

## Descargar el paquete.

Usted puede descargar la versión mas reciente este proyecto desde la siguientes **URLs**:

<a href="https://github.com/mundoSICA/Magento_1.6.x_translation_es_MX/tarball/master" class="button icon arrowdown">Download as <b>.tar.gz</b></a>
<a href="https://github.com/mundoSICA/Magento_1.6.x_translation_es_MX/zipball/master" class="button icon arrowdown">Download as <b>.zip</b></a>

## Agrega el SlugBehavior en tu aplicación.

Agrega el archivo `SlugBehavior.php` en la carpeta `app/Model/Behavior/`.

## Edita tu tabla de base de datos.

En este caso teníamos artículos con los siguientes campos.

 - id - Identificador (campo auto incrementable)
 - nombre - Es campo es único también.
 - contenido - texto el cual podría ser vació.

Es importante comentar que el campo nombre es único ahora solo faltaria agregar otro campo único con el nombre `slug_dst`.

Como **Tip** te recomiendo configuraren tu  base de datos el campo `slug_dst` como indice, esto por cuestiones de rendimiento ya que al generar `slug_dst` como indice el motor **SQL** genera mas rápido las consultas:

      ALTER TABLE `articulos` ADD INDEX ( `slug_dst` ) ;

## Descripción de argumentos:

#### slug\_src:

Por defecto toma el campo que hayamos definido como [**displayField**](http://book.cakephp.org/view/1062/displayField),ademas este campo deberá ser un campo único en la tabla, el cual sufrirá la trasformación **slug** para ser almacenado como `slug_dst`.


	#Definiendo el campo $displayField
	var $displayField = 'titulo';
	#Agregamos el comportamiento `Slug` entre la lista de comportamientos
	var $actsAs = array('Slug');

#### Definición explicita del campo a trasformar

	#Agregamos el comportamiento `Slug` entre la lista de
	#comportamientos, esta vez con defincion explita del
	#campo a traformar slug_src
	var $actsAs = array('Slug'=>array('slug_src' => 'titulo'));


Cabe mencionar que si definimos el **displayField** y también definimos explícitamente el campo a trasformar `slug_src` la opción que tomara sera la explicita, veamos un ejemplo:

	var $displayField = 'nombre';
	var $actsAs = array('Slug'=>array('slug_src' => 'titulo'));

El campo que tomara para trasformarlo sera `titulo` (por encima de `nombre`) ya que de esta forma estaremos diciendo explícitamente que el `slug_src` deberá ser el campo `titulo`.


#### slug\_dst "destino del campo slug":

Deberás agregar el campo slug_dst como indice:

	ALTER TABLE `talleres` ADD `slug_dst` VARCHAR( 80 ) NOT NULL AFTER `id` ,
	ADD INDEX ( `slug_dst` ) 

El resultado de la trasformación **slug**  la cual en este caso seria 'algun-articulo', se almacenara automáticamente en este campo y desde ahí nuestro sistema podrá responder desde una url similar a:

	#accediendo a alguna acción para el articulo "algún articulo"
	http://mi-sitio/articulos/alguna_accion/algun-articulo


> El comportamiento Slug revisa si la tabla contiene el campo `slug_dst` para almacenar esta información automáticamente.


#### slug\_dst explícito.

Ahora sí por alguna razón necesitas(ó simplemente deseas) que almacenar el valor `slug_dst`  en algún campo distinto por ejemplo `seo_url`, lo que tienes que hacer es definir explícitamente el campo `slug_dst`.

 
	var $actsAs = array('Slug'=>array('slug_dst' => 'seo_url'));

En este caso el valor 'algun-articulo' quedaria almacenado en el campo `seo_url` de nuestra tabla `articulos`.


#### max\_len Longitud máxima:

Longitud máxima que puede llegar a tomar `slug_dst`.

	var $actsAs = array('Slug'=>array('max_len'=>30));
	#esto nos genera
	Articulo.slug_dst = "este-es-un-articulo-sobre-el-c"

#### wd\_separator separador de palabras:

Separador de palabras `wd_separator` (words separator) es el carácter separador de palabras,el valor por defecto es el guion alto(-), pero tu puedes definir otro por ejemplo:

	var $actsAs = array('Slug'=>array('wd_separator'=>'_'));

Esto nos generaría en `slug_dst` el valor de `algun_articulo`(en lugar de `algun-articulo`)

	var $actsAs = array('Slug'=>array('wd_separator'=>'.'));

Esto nos generaría en `slug_dst` el valor de `algun.articulo`.


#### extension\_active Activación de extensión:

Por defecto false, si activamos este campo agrega la extensión '.html', reduciendo 5 caracteres el campo max\_len (la longitud de '.html').

	var $actsAs = array('Slug'=>array('extension_active'=>true));


En este caso el valor `slug_dst` seria `algun-articulo.html` agregando al final la extensión `.html`.


## Ejemplo concreto de configuración:

Entonces definimos la siguiente tabla:

	CREATE TABLE IF NOT EXISTS `articulos` (
		`id` INT( 5 ) PRIMARY KEY NOT NULL UNIQUE AUTO_INCREMENT,
		`nombre` VARCHAR( 80 ) NOT NULL UNIQUE,
		`slug_dst` VARCHAR( 50 ) NOT NULL UNIQUE,
		`contenido` VARCHAR( 150 ) DEFAULT NULL
	) ENGINE=InnoDB DEFAULT CHARSET=latin1 AUTO_INCREMENT=1 ;

En nuestro modelo de **Articulo**(`APP/Model/Behavior/SlugBehavior.php`) definimos el comportamiento **slug** de la siguiente manera:

	<?php
	class Articulo extends AppModel{
		var $name = 'Articulo';
		
		#campo a mostrar
		var $displayField = 'nombre';
		
		#Definición del comportamiento slug
		var $actsAs = array( 'slug'=>array('extension_active'=>true));
	}

El código anterior hace que el campo nombre(definido como `displayField`) sera trasformado con la forma `slug` y sera almacenado en el campo `slug_dst` para que de esta forma podamos realizar las consultas.

### Accediendo a los valores desde slug

Finalmente en nuestro controlador podemos llamar a las funciones slug:

	#leyendo un registro a partir del slug
	$this->Articulo->readBySlug($slug);

	#obteniendo el id del articulo a partir del slug
	$id = $this->Articulo->primaryKeyBySlug(($slug));
	
	
Para las demás funciones el comportamiento slug realiza las operaciones necesarias por ejemplo si deseamos guardar un registro basta con hacer:

	#agregando
	$this->Articulo->save($this->data);
	
Donde en $this->data deberá contener el campo `displayField`(o el `slug_sr` que hallamos definido) y automáticamente el comportamiento nos va a generar el `slug_dst`, _¿fácil no?_.



## Cambios futuros.

Este proyecto actualmente esta en la versión `1.0` los cambios en la siguiente versión( 1.1 ) se esperan que sean:

 - Agregar **callBack slug_prefix**.
 - Agregar **callBack slug_postfix**.
 - Soporte para extensiones distintas a html, p.e.(`xhml`, `xml`).

Por el momento no he abordado el problema si tienes alguna idea bienvenida sea


## Hackers.

Si quieres comprender mas te remiendo seguir la siguiente documentación:

> <http://book.cakephp.org/2.0/en/models/behaviors.html>

