def count = 0
def lastSuccessfulBuild = 'null'
def lastBuild = 'null'
def currentBuild = 'null'
def buildSucceed = false
def brokenCommit = 'null'

//Retrieve Commit hash
// = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
pipeline{
    agent any  
        stages{

            stage('Setup'){
                steps{
                     script{
                        if (!fileExists('Count.txt')) {
                        writeFile file: './Count.txt', text: "$count"
                        }

                        if (!fileExists('lastSuccessfulBuild.txt')) {
                        writeFile file: './lastSuccessfulBuild.txt', text: "$lastSuccessfulBuild"
                        }

                        countRetrieved = readFile('Count.txt').trim() 
                        count = countRetrieved as int
                        echo "Count number is $count"
                 }
                }
               
            }
            stage('Count Commit'){
                when {
                    expression{count<8}
                }
                steps{
                    script{
                        echo "Increment count"
                        count+=1
                    }
                 
                }
            }
            stage('Git Bisect'){
                when {
                    expression {count>=8 && buildSucceed==false}
                }
                steps{
                    script{
                        brokenCommit = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)

                    }
                    sh "git bisect start ${brokenCommit} ${lastSuccessfulBuild}"
                    sh "git bisect run mvn clean test" 
                    sh "git bisect reset"
                }

            }
            stage('Build'){
                when {
                    expression{ count>=8 && buildSucceed==true}
                }
                steps{
                    sh'./mvnw clean compile'
                }
            }
            stage('Test') {
                when {
                    expression{ count>=8 && buildSucceed==true}
                }
                steps {
                    sh './mvnw test'
                }
            }
            stage('Package'){
                when {
                    expression{ count>=8 && buildSucceed==true}
                }
                steps{
                    sh './mvnw package'
                }
            }
            
        }

    post{

        success{
            echo 'Success build'

        }
        failure {
            echo 'Failed build'

        }
    }
    


}