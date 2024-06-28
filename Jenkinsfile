pipeline {
    agent any
    
    tools {
        nodejs 'node21'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/Akki055/3-Tier-Full-Stack.git'
            }
        }
        
        stage('Install package Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage('unit Test') {
            steps {
                sh "npm test"
            }
        }
        
        stage('Trivy Scan') {
            steps {
                sh "trivy fs --format table -o fs-report.html ."
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Campground -Dsonar.projectNmae=Campground"
                }
            }
        }
        
        stage('Docker Build & Tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'doc-cred') {
                        sh "docker build -t akki058/campg:latest ."
                    }
                }
            }
        }
        
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format table -o fs-report.html akki058/campg:latest"
            }
        }
        
        stage('Docker Deployment') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'doc-cred') {
                        sh "docker run -d -p 3000:3000 akki058/campg:latest"
                    }
                }
            }
        }
    }
}
