pipeline {
    agent {
        node {
            label 'maven-slave'
        }
    }

    environment {
        PATH= "/opt/apache-maven-3.9.7/bin:$PATH"
    }    

    stages {
        stage('build') {
            steps {
                sh 'mvn clean deploy'
            }
        }
    }
}
