<h1 align="center">Prueba 2</h1>

Prueba 2 - Despliegue de una aplicación Django y React.js Elaborar el deployment dockerizado de una aplicación en django (backend) con frontend en React.js contenida en el repositorio. Es necesario desplegar todos los servicios en un solo docker-compose. Se deben entregar los Dockerfiles pertinentes para elaborar el despliegue y justificar la forma en la que elabora el deployment (supervisor, scripts, dockercompose, kubernetes, etc) Subir todo lo elaborado a un repositorio (github, gitlab, bitbucket, etc). En el repositorio se debe incluir el código de la aplicación y un archivo README.md con instrucciones detalladas para compilar y desplegar la aplicación, tanto en una PC local como en la nube (AWS o GCP).

Para poder desplegar correctamente la aplicación, es necesario previamente tener instalador Docker y Docker-Compose.

1. Asegurarse de que los directorios backend y frontend y el docker-compose.yml previamente mostrado dentro de un mismo directorio, por ejemplo, ``app``.

2. Crear un archivo .env en el directorio backend con la siguiente linea:

```
SQL_DATABASE=postgres
```

3. Con el directorio app siendo el working directory, orquestrar los contenedores con:

```
docker-compose up --build
```

El comando puede llegar a requerir permisos de administrador.

La API de la aplicación debería correr en el puerto 8000, la base de datos en el puerto 5432 y el frontend en el puerto 3000. En caso de que el firewall bloquee el tráfico TCP, se puede permitirlo con el siguietne comando:

```
sudo ufw allow 5432/tcp
```