# Campos Relacionados (Odoo v11)
## Descripción
Muchas veces a la hora de programar en Odoo nos encontraremos con casos en lo que queremos trabajar  con campos que se encuentran en otros modelos. Para poder hacerlo Odoo nos facilita la posibilidad de usar campos relacionados que lo que viene hacer de forma resumida es enlazar un campo del modelo X con otro campo del modelo Y. Para poder acceder a la información que contienen habrá que usar la siguiente secuencia
### Estructura
Simplemente establece el argumento del nombre relacionado con su modelo.
```python
rel_field = fields.Char(string='Name',related='partner_id.name')
```
**rel_field** nombre que le ponemos al campo.
**string=' '** etiqueta que tendrá nuestro campo.
**related** declaración de la relación.
**fields.Char** indicamos el tipo de campo que vamos a otorgarle (este debe coincidir con el que hereda)

 El parámetro __store__ nos permite almacenar el valor en la base de datos.
```c
rel_field = fields.Char(string='Name',store=True,related='partner_id.name')
```
## Ejemplo
Para explicar lo mencionado anteriormente vamos a relacionar un campo del modelo de Producto con un campo que se encuentra en el modelo de Facturas.

### 1.- Localizar el nombre del campo que queremos relacionar
Para este caso seleccionaremos el campo  __Tipo de Producto__ del cual debemos coger el nombre que le da Odoo a dicho campo para ello posicionamos nuestro cursor encima del nombre y aparecerá una pantalla de color negro con la información del campo.

<img  src="https://raw.github.com/alejandroasc96/Documentacion//campoRelacionadoOdoo/Fotos/viendoCampoType.png"  width="700">

Una vez hecho estoy nos dirigiremos a nuestro modelo donde nos crearemos una nueva clase que hereda de account.invoice.line y añadiremos nuestro campo relacionado.

<img  src="https://raw.github.com/alejandroasc96/Documentacion//campoRelacionadoOdoo/Fotos/vistaModelo.png"  width="700">

A continuación crearemos la vista que va a contener el nuevo campo dentro de las líneas de factura.

<img  src="https://raw.github.com/alejandroasc96/Documentacion//campoRelacionadoOdoo/Fotos/vistaView.png"  width="700">

Y para finalizar dado que estamos usando la vista de Producto y Factura habrá que añadirlas a nuestro manifest

<img  src="https://raw.github.com/alejandroasc96/Documentacion//campoRelacionadoOdoo/Fotos/vistaManifest.png"  width="700">

### Consejo
Con esta sentencia **` 'application': 'True',`** conseguimos que nuestra aplicación esté en el listado principal de nuestro Odoo por lo que nos facilitará la búsqueda.
### Resultado:

**Vista sin modificar:**

<img  src="https://raw.github.com/alejandroasc96/Documentacion//campoRelacionadoOdoo/Fotos/vistaResultadoSinModificar.png"  width="700">

**Vista modificada**

<img  src="https://raw.github.com/alejandroasc96/Documentacion//campoRelacionadoOdoo/Fotos/vistaresultado.png"  width="700">
