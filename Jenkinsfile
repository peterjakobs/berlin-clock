pipeline {
    agent any
    tools {
        jdk 'jdk8'
        maven 'maven3'
    }
    stages {
        stage('install and sonar parallel') {
            steps {
                parallel(
                        install: {
                            sh "mvn -U clean test cobertura:cobertura -Dcobertura.report.format=xml"
                        },
                        sonar: {
                            /* withSonarQubeEnv('mysonar')
                             sh "mvn sonar:sonar -Dsonar.host.url=${env.SONARQUBE_HOST}" */
                            sh "mvn sonar:sonar -Dsonar.host.url=http://jenkins_mysonar:9000"
                        }
                )
            }
            post {
                always {
                    junit '**/target/*-reports/TEST-*.xml'
                    step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
                }
            }
        }
       stage ('deploy'){
            steps{
                configFileProvider([configFile(fileId: '8b880586-ab28-40c7-be01-b12932c5ed98', variable: 'SETTINGS')]) {
                    sh "mvn -s $SETTINGS deploy -DskipTests -Dartifactory_url=http://jenkins_artifactory:8082/artifactory/example-repo-local"
                }
            } 
        }
    }
}
