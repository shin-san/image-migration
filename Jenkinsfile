
pipeline {
    
    agent {
        node {
            label 'insert-linux-agent here'
        }
    }

    environment {
        HTTPS_PROTOCOL = "https://"

        REGISTRY1_URL = ""
        REGISTRY1_CREDS = credentials("registry1-creds")

        REGISTRY2_URL = ""
        REGISTRY2_CREDS = credentials("registry2-creds")

    }

    stages {
        
        stage ("Checkout") {
            steps {
                repo = checkout scm
            }
        }

        stage ("Migrate Registry to Another") {
            steps {
                script {

                    // login to registry 1
                    withDockerRegistry(credentialsId: "registry1-creds", url: "${HTTPS_PROTOCOL}${REGISTRY1_URL}") {
                        // get all images
                        def images = sh("docker images | awk '{print $1,$4}'", returnStdout:true).trim()

                        // iterate through images and deploy individually
                        images.each{ image -> 
                            key = image.split(",")[0]
                            value = image.split(",")[1]
                            //pull source-image from registry 1
                            sh 

                            //tag the image
                            sh "docker tag ${currentImage.imageName()} ${REGISTRY2_URL}/${key}:${value}"

                            //push target-image to registry 2
                            docker.withDockerRegistry(credentialsId: "registry2-creds", url: "${HTTPS_PROTOCOL}${REGISTRY2_URL}") {
                                sh "docker push ${REGISTRY2_URL}/${key}:${value}"
                            }
                        }
                    }
                }
            }
        }
    }
}