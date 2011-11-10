#Slug Behavior.
###Enables a model object to sluggable behavior.


## Descripción de argumentos:

#### slug\_src:

Por defecto toma el campo que hayamos definido como [**displayField**](http://book.cakephp.org/view/1062/displayField), este debera ser un campo uníco en la tabla, el cual sufrira la trasformación **slug** para ser almacenado como `slug_dst`.

	var $displayField = 'titulo';
	#esto nos genera
	Articulo.titulo = "Este es un articulo sobre el comportamiento Slug"
	Articulo.slug_dst = "Este-es-un-articulo-sobre-el-comportamiento-Slug"
	#Otra forma de hacerlo es de la siguiente manera
	var $actsAs = array('Slug'=>array('slug_src' => 'titulo'));

Cabe mencionar que si definimos el **displayField** y por otra parte en la configuramos **slug_src** la opción que tomara sera esta ultima, veamos un ejemplo.

	var $displayField = 'nombre';
	var $actsAs = array('Slug'=>array('slug_src' => 'titulo'));

El campo que tomara sera `titulo` (por encima de `nombre`) ya que de esta forma estaremos diciendo explicitamente que el `slug_src` debera ser el campo `titulo`.


#### slug\_dst:

Este campo tambien debe ser unico en la tabla y sera el resultado de la trasformación slug sobre `slug_dst` que sera almacenado en la base de datos automaticamente.
 
	var $actsAs = array('Slug'=>array('slug_src' => 'seo_url'));
	#esto nos genera
	Articulo.slug_dst = "Este es un articulo sobre el comportamiento Slug"
	Articulo.seo_url = "este-es-un-articulo-sobre-el-comportamiento-slug" .


#### max\_len:

Longitud máxima que puede llegar a tomar slug\_dst.

	var $actsAs = array('Slug'=>array('max_len'=>30));
	#esto nos genera
	Articulo.slug_dst = "este-es-un-articulo-sobre-el-c"

#### wd\_separator:

Separador de palabras `wd\_separator` (words separator) es el caracter separador de palabras, por defecto(-).

	var $actsAs = array('Slug'=>array('wd_separator'=>'_'));
	#esto nos genera
	Articulo.slug_dst = "este_es_un_articulo_sobre_el_comportamiento_slug" .

####extension\_active:

Por defecto false, si activamos este campo agrega la extensión '.html', reduciendo 5 carácteres el campo max\_len (la longitud de '.html').

	var $actsAs = array('Slug'=>array('extension_active'=>true));
	#esto nos genera
	Articulo.slug_dst = "este-es-un-articulo-sobre-el-comportamiento-slug.html"


## Ejemplo de configuración:

Imaginemos que queremos hacer que nuestros articulos que nos gustaria que tuviera urls amigable es decir que sobre el titulo podieramos es decir:
	
	#visualizando un articulo
	http://mi-sitio/articulos/ver/algun-articulo.html
	
	#Editando un articulo
	http://mi-sitio/articulos/editar/algun-articulo.html
	
	#Borrando un articulo
	http://mi-sitio/articulos/borrar/algun-articulo.html

Entonces definimos la siguiente tabla:

	CREATE TABLE IF NOT EXISTS `articulos` (
		`id` INT( 5 ) PRIMARY KEY NOT NULL UNIQUE AUTO_INCREMENT,
		`nombre` VARCHAR( 80 ) NOT NULL UNIQUE,
		`slug_dst` VARCHAR( 50 ) NOT NULL UNIQUE,
		`contenido` VARCHAR( 150 ) DEFAULT NULL,
		`created` DATETIME NOT NULL,
	) ENGINE=InnoDB DEFAULT CHARSET=latin1 AUTO_INCREMENT=1 ;

En nuestro modelo de **Articulo** definimos el comportamiento **slug** de la siguiente manera:

	<?php
	class Articulo extends AppModel{
		var $name = 'Articulo';
		
		#campo a mostrar
		var $displayField = 'nombre';
		
		#Definición del comportamiento slug
		var $actsAs = array( 'slug'=>array('extension_active'=>true));
	}

El código anterior hace que el campo nombre(definido como `displayField`) sera trasformado con la forma `slug` y sera almacenado en el campo `slug_dst` para que de esta forma podamos realizar las consultas.

Finalmente en nuestro controlador podemos llamar a las funciones slug:

	#leyendo un registro a partir del slug
	$this->Articulo->readBySlug($slug);
	
	#obteniendo el id del articulo a partir del slug
	$id = $this->Articulo->primaryKeyBySlug(($slug);
	
	
Para las demas funciones el comportamiento slug realiza las operaciones necesarias por ejemplo si deseamos guardar un registro basta con hacer:

	#agregando
	$this->Articulo->save($this->data);
	
Donde en $this->data debera existir el campo nombre y automaticamente el comportamiento nos va a generar el `slug_dst` slug destino al tiempo que realiza las validaciones necesarias, _¿facil no?_.

