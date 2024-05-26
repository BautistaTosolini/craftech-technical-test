<h1 align="center">Prueba 3</h1>

Prueba 3 - CI/CD Dockerizar un nginx con el index.html default. Elaborar un pipeline que ante cada cambio realizado sobre el index.html buildee la nueva imagen y la actualize en la plataforma elegida. (docker-compose,
swarm, kuberenetes, etc.) Para la creacion del CI/CD se puede utilizar cualquier plataforma (CircleCI, Gitlab, Github, Bitbucket.)

Para la realización de la prueba utilicé una pipeline en Jenkins la cual se triggerea a través de un Webhook de Github cuando se pushea un commit al repositorio.

Código de la Pipeline:

```
pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        REPOSITORY_NAME = 'BautistaTosolini/test-cicd-nginx'
        IMAGE_NAME = 'cicd-nginx-image'
        CONTAINER_NAME = 'cicd-nginx-container'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git url: "https://github.com/${REPOSITORY_NAME}", branch: 'main'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = "${REPOSITORY_NAME.toLowerCase()}:${env.BUILD_ID}"
                    docker.build(imageName)
                }
            }
        }
        
        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        def image = docker.image("${REPOSITORY_NAME.toLowerCase()}:${env.BUILD_ID}")
                        image.push()
                    }
                }
            }
        }
        
        stage('Deploy Container') {
            steps {
                script {
                    // Limpiar contenedores previos (|| true permite que el runner se siga ejecutando aún si no hay un contenedor en ejecución)
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                    
                    sh "docker run -d -p 3000:80 --name ${CONTAINER_NAME} ${REPOSITORY_NAME.toLowerCase()}:${env.BUILD_ID}"
                }
            }
        }
    }
    
    // Limpiar el workspace luego de la ejecución de la pipeline
    post {
        always {
            cleanWs()
        }
    }
}
```

La pipeline se divide en 4 stages:
1. Checkout: clona el repositorio de la rama Main.
2. Build Docker Image: crea la imagen del proyecto.
3. Push Docker Image to DockerHub: auto explicativo.
4. Deploy Container: limpia los contenedores previos y crea uno con la imagen nueva. La aplicación se estará ejecutando en el puerto 3000.

Para poder implementar esta pipeline se debe instalar previamente Java, Docker y Jenkins, luego agregar el usuario de jenkins al grupo de Docker.

```
sudo usermod -aG docker jenkins
```

Iniciar el servicio de jenkins, ingresar al puerto 8080 de la máquina host e iniciar sesión. Una vez dentro, instalar, además de los plugins recomendados, el plugin de Docker Pipeline para luego comenzar a crear dicha Pipeline.

A continuación se deben agregar las credentials de DockerHub para que la Pipeline pueda iniciar sesión en la plataforma y pushear la imagen.

Al crear la Pipeline, seleccionar la opción de ``Github Project``, agregar el link del repo del proyecto, luego seleccionar ``GitHub hook trigger for GITScm polling``. Dentro del github, en la configuración del repositorio agregar un nuevo webhook con la dirección de jenkins en la máquina host de la siguiente manera: ``http://direcciónip:8080/github-webhook/``

Finalmente en el apartado de pipeline, agregar la pipeline previamente mostrada.