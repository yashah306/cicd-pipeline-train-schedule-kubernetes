 

pipeline {
   
   agent any
    options {
        timeout(time: 1, unit: 'HOURS')
        sendSplunkConsoleLog()
    }
    environment {
        
        DOCKER_IMAGE_NAME = "yashah1/train-schedule"
    }
    stages {
        stage('Build') {

            steps {
                sendSplunkFile excludes: '', includes: '*.log', publishFromSlave: true, sizeLimit: '100MB'

                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
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
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        

        }
    }
}
        

