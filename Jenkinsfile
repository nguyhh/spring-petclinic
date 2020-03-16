
pipeline{
    agent any
    stages{

        stage('Build'){
            steps{
                sh'./mvnw package'
            }
        }
        stage('Clean'){
            steps{
                sh'mvn clean'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Package'){
            steps{
                sh 'mvn package'
            }
        }
        stage('Deploy'){
            when {
                branch 'master'
            }
            steps{
                sh 'mvn deploy'
            }
        }
    }
    post{

        success{
            mail to: 'nguyenhaiha1224@gmail.com',
            subject: "Sucess Pipeline: ${currentBuild.fullDisplayName}",
            body: "Build success ${env.BUILD_URL}"

        }
        failure {

            mail to: 'nguyenhaiha1224@gmail.com',
            subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
            body: "Something is wrong with ${env.BUILD_URL}"   

        }
    }
    
}