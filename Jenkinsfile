pipeline {
    agent any
    environment {
        SONAR_TOKEN= 'a3f3e8803a360e23d9e0cb74fc19a6b280b602a8'
        APP_HOME='/home/app'
        PRAGRA_BATCH='devs'
    }
    options { 
        quietPeriod(30) 
    }
    parameters { 
            choice(name: 'ENV_TO_DEPLOY', 
            choices: ['ST', 'UAT', 'STAGING'], description: 'Select a Env to deploy') 

             booleanParam(name: 'RUN', defaultValue: true, description: 'SELECT TO RUN')
    }
    triggers {
        pollSCM('* * * * *')
    }
    tools{
        maven  'm2'
        jdk 'jdk11'
    }

    stages {
        stage('Clean Ws') {
            steps{
                cleanWs()
            }
        }
        stage('Git CheckoutOut'){
            steps{
                checkout scm
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
       stage('Static Code Analaysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonartoken', installationName: 'sonarcloud') {
                    sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar'
                }
            }
        }
         stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

         stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
            stage('Publish to Arctifactory') {
                steps {
                    rtUpload (
                            serverId: 'art1',
                                        spec: '''{
                                        "files": [
                                    {
                                            "pattern": "*dummy*.jar",
                                                 "target": "libs-release-local/dummy/"
                                    },
                                    {
                                             "pattern": "pom.xml",
                                             "target": "libs-release-local/dummy/"
                                    }
                                 ]
                                    }''',
 
             
             buildName: 'holyFrog',
             buildNumber: '42',
             // Optional - Only if this build is associated with a project in Artifactory, set the project key as follows.
                 project: 'my-project-key'
                )
                }
            }
    }
    
}
    
