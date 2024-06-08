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
    
        // stage('SonarQube analysis') {
        //     environment {
        //         scannerHome = tool 'sonarcloud-scanner'
        //     }
        //         steps{
        //             withSonarQubeEnv('sonarcloud-server') { // If you have configured more than one global server connection, you can specify its name
        //             sh "${scannerHome}/bin/sonar-scanner"
        //         }
        //     }
        // }
    }
}
