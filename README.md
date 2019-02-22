


# Uso de Docker 
(Facilitando la vida del programador)

  

![](https://lh4.googleusercontent.com/pDdUiWy3bw-JyaCsSbBayV-0bH2xZHXouD0PIikVxIyPZQXaSPoE---Ky_FVvDzRb_AX4phRfsU0Po1RWXL8wRLsFW-YFg_bxBCQEC_dSWr3vy8Ff465AKIuRHRYbQa6CkiKmCx7)



## ¿Qué es Docker? 
Docker es un programa de código abierto que permite que una aplicación Linux y sus dependencias se empaqueten como un contenedor.

La virtualización basada en contenedores aísla las aplicaciones entre sí en un sistema operativo (OS) compartido. Este enfoque estandariza la entrega del programa de la aplicación, permitiendo que las aplicaciones se ejecuten en cualquier entorno Linux, ya sea físico o virtual. Dado que comparten el mismo sistema operativo, los contenedores son portátiles entre diferentes distribuciones de Linux, y son significativamente más pequeños que las imágenes de máquinas virtuales (VM).
## Instalando docker 
###  Paso 1 : Eliminar posibles versiones antiguas de Docker
```
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```
### Paso 2 : Instalar Docker CE
1. Habŕa que asegurarnos que el paquete  ***apt*** esté actualizado.
```
$ sudo apt-get update
```
2. Para instalar Docker en Ubuntu basta con que ejecutemos el siguiente comando:
```
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```
3. Reiniciar Docker

Con la finalidad de que todos los cambios sean tomados adecuadamente es conveniente reiniciar docker utilizando:
```
sudo service docker restart
```
4. Verificar que Docker CE está instalando correctamente.
```
$ sudo docker run hello-world
```

5. Añadir tu usuario al grupo de Docker.
```
sudo gpasswd -a ${USER} docker
```
## Instalando Odoo + Postgres en Docker
Una vez hayamos instalado docker, debemos crear el archivo  **docker-compose.yml** en el directorio de nuestra preferencia, el mismo contendrá básicamente toda la información necesaria para hacer deploy de nuestro servicio con Odoo.

`nano docker-compose.yml`

Este archivo va a contener lo siguiente:
```
version: '2'
services:
  db:
    image: postgres:9.4
    volumes:
      - ./db-data:/var/lib/postgresql/data/pgdata
    environment:
    - POSTGRES_PASSWORD=odoo
    - POSTGRES_USER=odoo
    - POSTGRES_DB=postgres
    - PGDATA=/var/lib/postgresql/data/pgdata
  web:
    image: odoo:11
    command: --dev=all
    depends_on:
      - db
    ports:
      - "8069:8069"
    volumes:
      - web-data:/var/lib/odoo
      - ./config:/etc/odoo
      - ./extra-addons:/mnt/extra-addons
volumes:
  db-data:
    driver: local
  web-data:
    driver: local
```
En dicho archivo podemos hacer los siguientes cambios para adaptarlo a nuestros requerimientos:

-   `image: odoo:11` : Puedes reemplaza odoo:11 por la versión que necesitas odoo:9 , odoo:10, odoo12 o simplemente odoo:latest para la última versión disponible.
-   `ports: - "8069:8069"`  : Reemplace el primer puerto por el puerto que desee, esto le ayudará a tener múltiples instancias de odoo corriendo al mismo tiempo, por ejemplo podría quedar así, `ports: - "8070:8069"`  o `ports: - "8071:8069"` y así sucesivamente
-   `image: postgres:9.4`  : También puede reemplazar la imagen de postgres que desea usar.
- `volumes:` gracias a esta etiqueta lo que estamos haciendo es mapear la carpeta virtual de Docker donde se encuentra nuestro módulo de odoo con la carpeta del sistema donde tenemos nuestro proyecto. **- ./extra-addons:/mnt/extra-addons** en este caso gracias al **./extra-addons** le estamos indicando que coja el propio directorio donde nos encontramos para crear una carpeta llamada extra-addons la cual va a estar mapeada con la carpeta dentro de docker en el directorio  **/mnt/extra-addons**.

En líneas generales con este **docker-compose.yml** invocamos un conjunto de contenedores que se relacionan entre sí, como lo son el contenedor de la versión de odoo y el contenedor de postgres, así mismo para el primer contenedor manifestamos que escuchara del puerto 8069 (y se podrá acceder del que le indiquemos) y además se monta un volumen local llamado extra-addons que se vinculará automáticamente con el /mnt/extra-addons del contenedor de odoo.

Por último se describe el usuario y contraseña a utilizar para postgres y se determina que cuando el ordenador huésped se reinicia el servicio de docker también lo hará, esto gracias al parámetro restart: always.
### Arrancando Odoo
1. Una vez creado nuestro compose se nos quedaría un fichero tal que así
![](https://lh4.googleusercontent.com/Ah9YAJH-z6_KxHI2naOop8-1IGzUMOYUEJWuEY-VI7Wd0QZiSxU3sWDcKcyYt3gJFZncEJQGPjcqjS7wtWFnN4CxevV7BRcBw3Rq55YSruqtmx1qg2dhypSCgAuYHI_s80nB6NLO)

2. Creado nuestro docker-compose.yml, debemos iniciar la instancia de Odoo, para ello desde la terminal nos ubicamos en el directorio donde está el archivo creado anteriormente y ejecutamos:
```
docker-compose up
```
Automáticamente se iniciará la descarga de los contenedores docker necesarios, se iniciará la base de dato y podremos acceder a nuestra instancia de odoo desde `localhost:8069` o el puerto que haya indicado. Una vez en ella tocará crear nuestra base de datos, para lo que debemos elegir el email, contraseña de acceso, lenguaje e idioma, además de seleccionar si queremos importar data de prueba para evaluar Odoo.
![](http://jmoral.es/assets/img/odoo/usuario/6.png)

### Añadiendo módulos externos a Odoo

El **docker-compose.yml** que creamos en pasos anteriores además de levantar las imagen de odoo y postgres necesaria también nos crea un volumen en nuestro directorio para poder añadir módulos externos a nuestra instancia.

**![](https://lh5.googleusercontent.com/5QskRN5t1xRjiWJ_V2g7qx0yuANvaMkOgNyyV_1GSMxYh9q6bNeYWVXVfn66qhn3ZUVPvJRClYk-1MHvi0D-Vw-kGeO5_hry_xTKNkuAgY_fwCQgrMusjqn8PA_PjIlM0zbuiprx)**
1. Para poder empezar a trabajar con un módulo propio de Odoo primero hay que darle permisos a la carpeta extra-addons.

```
sudo chmod -R alejandroasc:alejandroasc extra-addons/ #reemplazar alejandroasc por su usuario
sudo chmod -R 755 extra-addons/
```
2. Luego en la misma ruta donde se encuentra nuestro compose debemos ejecutar el siguiente comando **docker exec -u root -t -i nombredelamaquina /bin/bash** este comando se ejecuta para entrar en la virtualizacion de docker para crear nuestro módulo. Para comprobar el nombre de nuuestra maquina de docker basta con abrir otro terminal y ejecutar el comando **sudo docker ps**
**![](https://lh3.googleusercontent.com/dCR3wdxVnJz0SFTvOVbkxHlAZhUH8p-uPQZnKHh8PUZmoJo1V0jWUPXIthC3OHfnvlacaE7ZRbblt6uu7oruod6R05v6cakwgTCQEs0lYKRfn65Cl8fDn9AUC7-6ZihG_Qx55iKu)**
3. Una vez ejecutadado el comando se nos quedaría la siguiente vista.
**![](https://lh5.googleusercontent.com/uIG-qHiEvC_ouI8hJZhqLWwlbk8kxDQkGdusy-1yiioV3Dzwf6qWfCWPE0ehUV5eUQFOOfCHbSLgXsSeNWgEQEagtLUbnOj7sa6wDUV7a-BDvaFQhER1jAyoS7XlZGETGIjZSI69)**
4. Luego habrá que ir a la siguiente ruta **/mnt/extra-addons** y ejecutamos el comando:
```
odoo scaffold nombreDelMóduloQueQeremosCrear.
```
**![](https://lh6.googleusercontent.com/LUkR3Wbfq4JpX5FnBaHgL2fgpyneZIGBRx_4bwsW-qUHvipTn0wR20Es5pELEhbCHeDB9oJHJaJo-4o7ZDa3_oUGNKnNhmsuzycV6CaicIcoVyJZazaGQIU3nEULpcHo1mAqe5ks)**

5. Una vez hecho esto si vamos a nuestro directorio inicial veremos que dentro de extra-addons se han creado los archivos correspondiente a nuestro módulo.
**![](https://lh5.googleusercontent.com/4yYTaqDcGBTgaB1YQ80-hvSiW1gPTAAb4dkabd3f8Fay_x4WtvbqamduQejQav4KIx6oYGxJCi6kBhrNhYjYk-d8dsGLU0JzEazGf8v7C3n8RRofe19Dvalqd1u25JmE8Lwmg9gq)**
6. Por último solo nos quedaría abrir el módulo en un editor como Visual Studio Code  y empezar a trabajar.
**![](https://lh5.googleusercontent.com/DvBHw3N6x1Rg0-SWdDXrNRnEzjYO85jFHxDkfn3DmLnjSTDJMpQni-s0YQ9RcUIfxVTWMdqVhiWWzgJXmVlUqT12DswQYnQgwCwnjSBersY9oii7Vk6_SsKMtlVVVyx7z5zV1sNt)**
## Comandos útiles

-   docker images #para ver la lista de imágenes descargadas.
    
-   docker ps #para ver la lista de contenedores en ejecución.
    
-   docker ps -a #para ver la lista de todos los contenedores ejecutados.
    
-   docker rm #para borrar contenedores en nuestro equipo.
    
-   docker rmi #para borrar imagenes en nuestro equipo.
    
-   docker sear ch #para buscar imágenes en el Docker Hub.
    
-   docker pull #para descargar imágenes desde Docker Hub.
    
-   docker-compose up #lee el compose y monta los dockers
    
-   docker tag bb38976d03cf yourhubusername/verse_gapminder:firsttry
    
-   docker push yourhubusername/verse_gapminder
    
-   docker run -it nombreDelContenedor #para arrancar un contenedor ya creado.
    
-   docker stop idContenedor #para parar un contenedor ya creado.
    
-   docker exec -u root -t -i nombredelamaquina /bin/bash #ejecutar maquina virtual Docker
