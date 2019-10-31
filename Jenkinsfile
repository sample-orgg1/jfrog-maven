pipeline {
    agent any
    def server = Artifactory.server('atuldevops.jfrog.io')
    def buildInfo = Artifactory.newBuildInfo()
    def rtMaven = Artifactory.newMavenBuild() 
    stages {
        stage('Code Checkout') { 
            steps {
                git credentialsId: 'githubID', url: 'https://github.com/itrainwarriors/jfrog-maven.git'
            }
        }
        stage('Build') { 
            steps {
              withMaven(jdk: 'JDK-1.8', maven: 'Maven-3.6.1') {
               sh 'mvn clean compile'
             }
            }
        }
        stage('Test') { 
            steps {
               withMaven(jdk: 'JDK-1.8', maven: 'Maven-3.6.1') {
               sh 'mvn test'
             }  
            }
        }
        stage('Artifactory configuration') { 
            steps {
              rtMaven.tool = 'Maven-3.6.1' // Tool name from Jenkins configuration
              rtMaven.deployer releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local', server: server
              rtMaven.resolver releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot', server: server
              rtMaven.deployer.deployArtifacts = false // Disable artifacts deployment during Maven run
            }
        }
        stage ('Install') {
            steps {
             rtMaven.run pom: 'pom.xml', goals: 'install', buildInfo: buildInfo
           }
        }
        stage ('Deploy') {
            steps {
            rtMaven.deployer.deployArtifacts buildInfo
           }
        }
        stage ('Publish build info') {
           steps {
            server.publishBuildInfo buildInfo
          }
        }
        stage('Docker Image') { 
            steps {
             sh 'echo docker image is build'   
            }
        }
        stage('Deploy to Dev') { 
            steps {
             sh 'echo Deploy to Dev'   
            }
        }
        stage('Deploy to prod') { 
            steps {
              sh 'echo Deploy to Prod'  
            }
        }
    }
}
