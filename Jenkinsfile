def registry = "https://trialzdfyx5.jfrog.io"


pipeline { 
    agent any 
 
    environment { 
        PATH = "/opt/maven/bin:$PATH" 
    } 
 
    stages { 
 
        stage("build") { 
            steps { 
                sh 'mvn clean package' 
            } 
        } 
 
        stage('SonarQube analysis') { 
            environment { 
                scannerHome = tool 'saidemy-sonar-scanner' 
            } 
 
            steps { 
                withSonarQubeEnv('SonarQube') { 
                    sh "${scannerHome}/bin/sonar-scanner" 
                } 
            } 
        } 

        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'
                    def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "artifact-cred"
                    def properties = "buildId=${env.BUILD_ID},commit=${GIT_COMMIT}"
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstaging/(*)",
                                "target": "sai-libs-release-local/{1}",
                                "flat": "false",
                                "props": "${properties}",
                                "exclusions": ["*.sha1", "*.md5"]
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
    } 
}

