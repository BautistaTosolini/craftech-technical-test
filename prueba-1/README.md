<h1 align="center">Prueba 1</h1>

Prueba 1 - Diagrama de Red Produzca un diagrama de red (puede utilizar
lucidchart) de una aplicación web en GCP o AWS y escriba una descripción de
texto de 1/2 a 1 página de sus elecciones y arquitectura.

El diseño debe soportar:

• Cargas variables

• Contar con HA (alta disponibilidad)

• Frontend en Js

• Backend con una base de datos relacional y una no relacional

• La aplicación backend consume 2 microservicios externos

El diagrama debe hacer un mejor uso de las soluciones distribuidas.

Para el diseño del diagrama de red, utilicé una estructura de Three Tier con la que me encuentro familiarizado. Comienza con una VPC con una Internet Gateway y un Load Balancer que distribuirá el tráfico entre la capa de presentación. Dicha capa tendrá subnets públicas donde residirá una instancia EC2 donde se ejecutará el frontend. También dichas subnets tendrán unas NAT Gateway para que el backend tenga acceso al exterior y pueda consumir los micoservicios externos pero a la vez prohibiendo la comunicación del exterior hacia el backend directamente. El backend se accede nuevamente a través de un Load Balancer y se encuentra dentro de una subnet privada. Finalmente está la capa de datos donde se utiliza el servicio de Amazon Aurora para la base de datos relacional y DocumentDB para la no relacional, todas dentro de una subnet privada. Dichas bases tienen sus respectivas réplicas en distintas AZ. El diagrama fue creado dentro de una misma región a través de solo 2 AZ, pero esta puede ser escalada a una mayor cantidad de AZ, según lo que sea necesario, para aumentar su disponibilidad y capacidad.

En el caso de que el frontend sean archivos estáticos como HTML y CSS, se podría reemplazar las instancias EC2 de la capa de presentación por el servicio S3 de AWS, lo cual a su vez nos permite eliminar el primer load balancer ya que no sería necesario, reduciendo así los costos.