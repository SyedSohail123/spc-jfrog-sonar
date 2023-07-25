pipeline {
    agent { label 'MAVEN_JDK8' }
    triggers { pollSCM('* * * * *') }
    tools {
        jdk 'JDK_17'
        maven 'MAVEN-3.9.3'
    }
    stages {
        stage('VCS') {
            steps {
                git url: 'https://github.com/SyedSohail123/spc-jfrog-sonar.git',
                    branch: 'main'
            }
        }
        // stage('sonarscanning') {
        //     steps {
        //        withSonarQubeEnv('SONAR_CLOUD') {
        //             sh 'mvn clean install sonar:sonar -Dsonar.organization=qtdevopssohail123 -Dsonar.token=8c15adacf466a5ccd721f4f7cdb2c4bf17df84ab -Dsonar.projectKey=qtdevopssohail123'  
        //        } 
        //     }
        // }
        stage('version') {
            steps {
                sh 'java -version'
                sh 'mvn --version' 
            }
        }
        stage('Publish artifacts to Jfrog') {
            steps {
                rtMavenDeployer (
                    tool: 'default'
                    id: "MAVEN_DEPLOYER",
                    serverId: "Jfrog_Devops",
                    releaseRepo: 'onepiece-libs-release-local',
                    snapshotRepo: 'onepiece-libs-snapshot-local'
                )
                rtMavenRun (                    
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER"
                )
                rtPublishBuildInfo (
                    serverId: "Jfrog_Devops"
                )
            }
        }
        stage('Archiving the artifacts & publishing test results') {
            steps {
                archiveArtifacts onlyIfSuccessful: true,
                            artifacts: '**/target/*.jar'
                junit testResults: '**/surefire-reports/TEST-*.xml' 
            }
        }
    }
}