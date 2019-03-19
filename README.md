
# Herencia

Para este ejemplo vamos a añadir un nuevo campo llamado **Fecha de caducidad** al formulario de producto para que se nos quede un resulta como el que se ve a continuación.

  

<img  src="https://raw.github.com/alejandroasc96/Documentacion/herenciaOdoo/Fotos/formularioProducto.png"  width="700">

Para aplicar los cambios en primera instancia deberemos conocer el nombre del campo que está encima de donde queremos colocar nuestro nuevo campo para ello vamos a **Ajustes** y dentro de esa ventana activamos el **Modo Desarrollador**.

<img  src="https://raw.github.com/alejandroasc96/Documentacion/herenciaOdoo/Fotos/modoDesarrollador.png"  width="700">

 Una vez activado este modo al dirigirnos a la pestaña del formulario si ponemos el ratón por encima de la etiqueta nos aparecerá el nombre del campo que en mi caso es **standard_price**
 
<img  src="https://raw.github.com/alejandroasc96/Documentacion/herenciaOdoo/Fotos/nombreCampo.png"  width="700">

Una vez que sepamos el nombre deberemos buscar dicho campo en las vistas de Odoo para saber donde aplicar nuestro cambio exactamente, para esto no iremos a la pestaña que se encuentra en la parte superior a la derecha y una vez allí entraremos en editar vista de formulario.

<img  src="https://raw.github.com/alejandroasc96/Documentacion/herenciaOdoo/Fotos/editarVistaFormulario.png"  width="700">

Una vez hecho esto nos parecerá la siguiente vista donde deberemos buscar el nombre de nuestro campo ( standard_price) en este caso dicho campo no se encuentra en  esta vista si no en una vista heredada llamada **product.template.commom.form**. Para ver dicha vista picaremos sobre el icono marcado.

<img  src="https://raw.github.com/alejandroasc96/Documentacion/herenciaOdoo/Fotos/vistaFormularioTemple.png"  width="700">

Una vez abierto la nueva vista y habiendo encontrado el nombre de nuestro campo deberemos recordar la información marcada con flechas (nombre del modelo **product.template** , ID externo **product.product_template_form_view**) así mismo como la estructura que está establecida en la vista de formulario.

<img  src="https://raw.github.com/alejandroasc96/Documentacion/herenciaOdoo/Fotos/vistaheredada.png"  width="700">

<img  src="https://raw.github.com/alejandroasc96/Documentacion/herenciaOdoo/Fotos/vistaheredada1.png"  width="700">

Una ves hecho esto lo que haremos  primero es crear un modelo  que hereda de **product.template** y donde nos creamos  el nuevo campo que en este caso se llama **fechaAle** que es de tipo **Date**.

<img  src="https://raw.github.com/alejandroasc96/Documentacion/herenciaOdoo/Fotos/modelo.png"  width="700">

Una vez creado nuestro modelo lo que haremos es crearnos nuestra vista que hace referencia a **product.product_template_form_view** (Véase las imágenes anteriores para ver el significado de las flechas).

<img  src="https://raw.github.com/alejandroasc96/Documentacion/herenciaOdoo/Fotos/vistaFormulario.png"  width="700">