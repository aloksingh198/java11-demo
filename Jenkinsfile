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
        stage('Publish Arctifactory'){
    rtMavenResolver (
    id: 'resolver',
    serverId: 'artifactory-1',
    releaseRepo: 'libs-release-local',
    snapshotRepo: 'libs-snapshot-local'
)  
 
rtMavenDeployer (
    id: 'deployer',
    serverId: 'Artifactory-1',
    releaseRepo: 'libs-release-local',
    snapshotRepo: 'libs-snapshot-local',
    // By default, 3 threads are used to upload the artifacts to Artifactory. You can override this default by setting:
    threads: 6,
    // Attach custom properties to the published artifacts:
    properties: ['version=v1', 'publisher=alok singh']
)
rtMavenRun (
    // Tool name from Jenkins configuration.
    tool: 'm2',
    pom: 'pom.xml',
    goals: 'clean install',
    // Maven options.
    opts: '-Xms1024m -Xmx4096m',
    resolverId: 'resolver',
    deployerId: 'deployer',
    // If the build name and build number are not set here, the current job name and number will be used:
    buildName: 'my-build-name',
    buildNumber: '17',
    // Optional - Only if this build is associated with a project in Artifactory, set the project key as follows.
    project: 'my-project-key'
)
}
    post {
        always {
            echo 'ALL GOOD '
        // }
    }


}
    