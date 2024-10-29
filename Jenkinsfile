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
                        // Set up SonarQube properties, if needed
                        def sonarProperties = """
                        sonar.projectKey=com.example:springboot
                        sonar.projectName=springboot
                        sonar.projectVersion=1.0
                        sonar.sources=src/main/java
                        sonar.tests=src/test/java
                        sonar.java.binaries=target/classes
                        """

                        // Write SonarQube properties to a file (optional)
                        writeFile file: 'sonar-project.properties', text: sonarProperties

                        // Execute the SonarQube analysis with credentials
                        withSonarQubeEnv(credentialsId: 'sonar-api') {
                            // Run the Sonar analysis command
                            sh 'mvn sonar:sonar -Dproject.settings=sonar-project.properties'
                        }

                        // Generate a readable report in text format
                        // Ensure that the project key matches the one used in sonar.projectKey
                        sh 'curl -s -u squ_a38d85245c5f022add095aa2e3770f0578a408a5: http://54.237.200.195:9000/api/issues/search?componentKeys=com.example:springboot&resolutions=NONE > /var/lib/jenkins/workspace/demo/sonar_report.json'

                        // Check if the report file was created
                        sh 'cat sonar-report.txt'  // Optional: Display the contents in the console for verification
                    }
                }
            }

            
             
        }
        }
        
