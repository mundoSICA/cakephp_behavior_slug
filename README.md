#Slug Behavior.
###Enables a model object to sluggable behavior.


## Descripción de argumentos:

#### slug\_src:

Por defecto `slug_src` este debera ser un campo unico en la tabla, el cual sufrira la trasformación **slug** para ser almacenado como `slug_dst`.
	
	var $actsAs = array('Slug'=>array('slug_src' => 'titulo'));
	#esto nos genera
	Articulo.titulo = "Este es un articulo sobre el comportamiento Slug"
	Articulo.slug_dst = "Este-es-un-articulo-sobre-el-comportamiento-Slug"

#### slug\_dst:

Este campo tambien debe ser unico en la tabla y sera el resultado de la trasformación slug sobre `slug_dst` que sera almacenado en la base de datos automaticamente.
 
	var $actsAs = array('Slug'=>array('slug_src' => 'seo_url'));
	#esto nos genera
	Articulo.slug_dst = "Este es un articulo sobre el comportamiento Slug"
	Articulo.seo_url = "este-es-un-articulo-sobre-el-comportamiento-slug" .


####max\_len:

Longitud máxima que puede llegar a tomar slug_dst.

	var $actsAs = array('Slug'=>array('max_len'=>30));
	#esto nos genera
	Articulo.slug_dst = "este-es-un-articulo-sobre-el-c"

####wd\_separator:

Caracter separadora de palabras, por defecto(-).

	var $actsAs = array('Slug'=>array('wd_separator'=>'_'));
	#esto nos genera
	Articulo.slug_dst = "este_es_un_articulo_sobre_el_comportamiento_slug" .

####extension\_active:

Por defecto false, si activamos este campo agrega la extensión '.html', reduciendo 5 carácteres el campo max\_len (la longitud de '.html').

	var $actsAs = array('Slug'=>array('extension_active'=>true));
	#esto nos genera
	Articulo.slug_dst = "este-es-un-articulo-sobre-el-comportamiento-slug.html"


## Ejemplo de configuración:

Imaginemos que queremos hacer que nuestros articulos(articles) que nos gustaria que tuviera urls amigable es decir que sobre el titulo podieramos es decir:
	
	#visualizando un articulo
	http://mi-sitio/articles/view/algun-articulo.html
	
	#Editando un articulo
	http://mi-sitio/articles/edit/algun-articulo.html
	
	#Borrando un articulo
	http://mi-sitio/articles/delete/algun-articulo.html


