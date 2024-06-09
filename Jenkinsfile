pipeline {
    /* Definimos el pipeline */
    environment {
        /* Definimos las variables de entorno */
        IMAGEN = "nginx" /* Cambiamos el nombre de la imagen de Docker a nginx */
        USUARIO = 'NEXUS_CREDENTIAL' /* Nombre de usuario de Docker Hub */
    }
    agent any /* Indicamos que el agente puede ser cualquiera de los disponibles, en este caso el que tenga Docker instalado */
    stages { /* Definimos las etapas del pipeline */
        stage('Clone') { /* Etapa de clonación del repositorio */
            steps {
                git branch: "main", url: 'https://github.com/berellajuan/test-pipleline.git' /* Clonamos el repositorio de GitHub */
            }
        }
        stage('Build') { /* Etapa de construcción de la imagen de Docker */
            steps {
                script {
                    newApp = docker.build "$IMAGEN:$BUILD_NUMBER" /* Construimos la imagen de Docker con un nombre único basado en el número de compilación */
                }
            }
        }

        stage('Test') { /* Etapa de pruebas */
            steps {
                script {
                    docker.image("$IMAGEN:$BUILD_NUMBER").inside('-u root') { /* Ejecutamos un contenedor de Docker con la imagen construida */
                           sh 'nginx -v' /* Cambiamos el comando a 'nginx -v' para verificar la versión de nginx dentro del contenedor */
                        }
                    }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'NEXUS_CREDENTIAL' , usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        docker.withRegistry('http://localhost:8083', "$USERNAME:$PASSWORD") {
                            newApp.push()
                        }
                    }
                }
            }
        }
        
        stage('Clean Up') { /* Etapa de limpieza */
            steps {
                sh "docker rmi $IMAGEN:$BUILD_NUMBER" /* Eliminamos la imagen de Docker localmente */
                }
        }
    }
}