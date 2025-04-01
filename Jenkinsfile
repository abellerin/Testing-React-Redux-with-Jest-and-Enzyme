pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/abellerin/Testing-React-Redux-with-Jest-and-Enzyme.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
                echo 'Build completado correctamente'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker build -t abellerin/Testing-React-Redux-with-Jest-and-Enzyme .'
                sh 'docker run -d --name Testing-React-Redux-with-Jest-and-Enzyme -p 8081:8080 abellerin/Testing-React-Redux-with-Jest-and-Enzyme'
            }
        }
    }
}
