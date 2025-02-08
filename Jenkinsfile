pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Original'){
            steps {
                echo 'Original'
            }
        }
        stage('Build') {
            steps {
                echo 'Building' 
                script {
                    if(fileExists('/var/lib/jenkins/workspace/JenkinsPipeline/index.html')){
                        echo 'File Not Exists'
                        error 'File Not Exists'
                    }
                }   
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying'
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'AWSUSER',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]){
                        sh(script: 'aws s3 cp /var/lib/jenkins/workspace/JenkinsPipeline/index.html s3://test-jenkins-bucket-ir')
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Testing'
                script {
                    def url = 'https://test-jenkins-bucket-ir.s3.ap-northeast-1.amazonaws.com/index.html'
                    def response = sh(script: "curl -s -o /dev/null -w '%{http_code}' '$url'",returnStdout: true)

                    if (response == '200') {
                        echo 'Test Passed'
                    } else {
                        echo response
                        error 'Test Failed'
                    }
                }
            }
        }
        stage('Release') {
            steps {
                echo 'Releasing'
            }
        }
    }
}
