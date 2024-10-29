pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/vikash-kumar01/demo-counter-app.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
        stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar-api') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                   }
                    
                }
            }
             stage('Generate SonarQube Report') {
            steps {
                script {
                    // Assuming SonarQube server is configured to allow report generation
                    // This command might vary depending on your SonarQube configuration
                    withSonarQubeEnv(credentialsId: 'sonar-api') {
                        // Run the Sonar analysis command
                        sh 'mvn sonar:sonar -Dproject.settings=sonar-project.properties'
                    }
                }
            }
        }
        }
        
}