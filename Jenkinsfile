pipeline {
    agent {
            label 'jenkins-slave'
}

    stages {
        stage('Build') {
            steps {
               sh """
               ls
               docker compose up -d
               docker tag ${JOB_NAME}-frontend gilni/java-frontend:v${BUILD_ID}
               docker tag ${JOB_NAME}-backend gilni/java-backend:v${BUILD_ID}
               docker images
               docker ps
               """
            }
        }
        stage('Test') {
            steps{
                sleep 30
                sh""" 
                docker exec ${JOB_NAME}-frontend-1 curl localhost:3000
                docker exec ${JOB_NAME}-backend-1 curl localhost:8080
                """
            }
        }
        stage('Deploy') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_creds', passwordVariable: 'docker_pass', usernameVariable: 'docker_username')]) {
                    sh ("docker login -u ${docker_username} -p ${docker_pass}")
                    sh "docker push gilni/java-backend:v${BUILD_ID}"
                    sh "docker push gilni/java-frontend:v${BUILD_ID}"
                    
                }
            }
        }
        stage('CleanUp'){
            steps {
               sh '''
                docker compose down
                docker rmi $(docker images -q) -f
               '''
               cleanWs()
            }
        }
    }
}