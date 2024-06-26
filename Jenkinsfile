def registry = 'https://nekabara.jfrog.io'
def imageName = 'nekabara.jfrog.io/valaxy-docker-local/ttrend'
def version   = '2.1.2'
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

        stage("Jar Publish") {
            steps {
                script {
                        echo '<--------------- Jar Publish Started --------------->'
                        def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog"
                        def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                        def uploadSpec = """{
                            "files": [
                                {
                                "pattern": "jarstaging/(*)",
                                "target": "mvn-libs-release/{1}",
                                "flat": "false",
                                "props" : "${properties}",
                                "exclusions": [ "*.sha1", "*.md5"]
                                }
                            ]
                        }"""
                        def buildInfo = server.upload(uploadSpec)
                        buildInfo.env.collect()
                        server.publishBuildInfo(buildInfo)
                        echo '<--------------- Jar Publish Ended --------------->'  
                
                }
            }   
        }

        stage(" Docker Build ") {
          steps {
            script {
               echo '<--------------- Docker Build Started --------------->'
               app = docker.build(imageName+":"+version)
               echo '<--------------- Docker Build Ends --------------->'
            }
          }
        }

        stage (" Docker Publish "){
            steps {
                script {
                   echo '<--------------- Docker Publish Started --------------->'  
                    docker.withRegistry(registry, 'jfrog'){
                        app.push()
                    }    
                   echo '<--------------- Docker Publish Ended --------------->'  
                }
            }
        }

        stage(" Deploy ") {
          steps {
            script {
                sh 'chmod +x ./kubernetes/deploy.sh'
                sh './kubernetes/deploy.sh'
            }
          }
        }
    }
}
