pipeline {

    agent any

    stages {

        stage('Git Checkout') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/vikash-kumar01/demo-counter-app.git'
                }
            }
        }

        stage('UNIT testing') {
            steps {
                script {
                    sh 'mvn test'
                }
            }
        }

        stage('Integration testing') {
            steps {
                script {
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }

        stage('Maven build') {
            steps {
                script {
                    sh 'mvn clean install'
                }
            }
        }

        stage('Static code analysis') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-api') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }

        stage('Generate SonarQube Report') {
            steps {
                script {
                    def sonarProperties = """
                    sonar.projectKey=com.example:springboot
                    sonar.projectName=springboot
                    sonar.projectVersion=1.0
                    sonar.sources=src/main/java
                    sonar.tests=src/test/java
                    sonar.java.binaries=target/classes
                    """

                    writeFile file: 'sonar-project.properties', text: sonarProperties

                    withSonarQubeEnv(credentialsId: 'sonar-api') {
                        sh 'mvn sonar:sonar -Dproject.settings=sonar-project.properties'
                    }

                    sh 'curl -s -u squ_a38d85245c5f022add095aa2e3770f0578a408a5: http://54.237.200.195:9000/api/issues/search?componentKeys=com.example:springboot&resolutions=NONE > sonar_report.json'
                }
            }
        }
    }

    post {
        always {
            echo 'Sending analysis report via email...'
            emailext (
                to: "sohildoshi333@gmail.com",
                subject: "SonarQube Analysis Report for ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: "The SonarQube analysis report for ${env.JOB_NAME} Build #${env.BUILD_NUMBER} is attached.\nPlease review the attached report for code quality and security findings.",
                attachLog: true,
                attachments:'sonar_report.json'
                attachmentsPattern: 'sonar_report.json'
            )
        }
    }
}
