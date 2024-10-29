pipeline {
    
    agent any 

    stages {
        
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/vikash-kumar01/demo-counter-app.git'
            }
        }

        stage('Unit Testing') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Integration Testing') {
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Static Code Analysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonar-api') {
                    sh 'mvn clean package sonar:sonar'
                }
            }
        }

        stage('Generate SonarQube Report') {
            steps {
                // Optional: Write sonar-project properties if not defined elsewhere
                writeFile file: 'sonar-project.properties', text: '''
                sonar.projectKey=com.example:springboot
                sonar.projectName=springboot
                sonar.projectVersion=1.0
                sonar.sources=src/main/java
                sonar.tests=src/test/java
                sonar.java.binaries=target/classes
                '''

                withSonarQubeEnv(credentialsId: 'sonar-api') {
                    sh 'mvn sonar:sonar -Dproject.settings=sonar-project.properties'
                }

                // Fetching the SonarQube report via API
                sh '''
                curl -s -u squ_a38d85245c5f022add095aa2e3770f0578a408a5: \
                http://54.237.200.195:9000/api/issues/search?componentKeys=com.example:springboot&resolutions=NONE \
                > sonar_report.json
                '''
            }
        }
    }

    post {
        always {
            echo 'Sending analysis report via email...'
            emailext (
                to: 'sohildoshi333@gmail.com',
                subject: "SonarQube Analysis Report for ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: """
                The SonarQube analysis report for ${env.JOB_NAME} Build #${env.BUILD_NUMBER} is attached.
                Please review the attached report for code quality and security findings.
                """,
                attachLog: true,
                attachmentsPattern: 'sonar_report.json'
            )
        }
    }
}
