pipeline {
    agent any
    environment {
        //be sure to replace "bhavukm" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "2981984/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
      
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('CanaryDeploy') {
           
          
            steps {
                kubernetesDeploy(
                    kubeconfigId: '23f6fe01-b5b5-4fec-84f5-66eedf6b9d92',
                    configs: 'pod.yml',
                    enableConfigSubstitution: true
                )
            }
        }
        
        }
    }

