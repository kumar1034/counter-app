pipeline {
    agent any

    stages {
        stage('Git Checkout'){
            steps {
                git branch: 'main', url: 'https://github.com/rohith1977/counter-app.git'
            }
        }
        stage('Maven Unit Testing'){
            steps {
                sh 'mvn test'
            }
        }
        stage('Integration Testing'){
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage('Maven Build Artifact'){
            steps {
                sh 'mvn clean install'
            }
        }
        stage('SonarQube analysis'){
            steps{
                script{  
                    withSonarQubeEnv(credentialsId: 'sonar-apikey') { 
                    sh 'mvn clean package sonar:sonar'
                    }
                   }
                    
                }
            }
        stage('Quality Gate status'){
            steps{
                script{  
                   waitForQualityGate abortPipeline: false, credentialsId: 'sonar-apikey'
                    }
                   }
                    
                }
        stage('Upload war file to Nexus'){
            steps{
                script{  

                    def read_pom_version = readMavenPom file: 'pom.xml'

                    def nexus-repo = read_pom_version.version.endWith("SNAPSHOT") ? "counterapp-snapshot" : "counterapp-release"
                    nexusArtifactUploader artifacts: 
                    [
                            [
                                artifactId: 'springboot', 
                                classifier: '', 
                                file: 'target/Uber.jar', 
                                type: 'jar'
                            ]
                    ], 
                    credentialsId: 'nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '3.137.172.102:8081/', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'counterapp-release', 
                    version: "${read_pom_version}"
                    }
                   }
                    
                }
    }
}
