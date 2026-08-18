<<<<<<< HEAD
pipeline {
    agent any
    stages {
        stage('checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/monisharumugam249-pixel/project4.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                configFileProvider([configFile(fileId: '1a93c845-4d8f-42ab-bf52-f86b627889df', variable:'MAVEN_SETTINGS')]) {
                    sh 'mvn deploy -s $MAVEN_SETTINGS'
                }
            }
        }
    }
}
=======
pipeline {
    agent { label "worker_1" } 

    stages {
        stage ('Checkout') {
            steps {
                checkout ([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                       url: 'https://github.com/monisharumugam249-pixel/project4.git',
                       credentialsId: 'git_token'
                    ]]
                ])
            }
        }

        stage ('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage  ('Test') {
            steps {
                sh 'mvn test'
            }
        }

       stage ('Deploy to Artifactory') {
            steps {
                configFileProvider([configFile(fileId: '1a93c845-4d8f-42ab-bf52-f86b627889df', variable: 'MAVEN_SETTINGS'
                )]) {
                    sh 'mvn deploy -s $MAVEN_SETTINGS'
                }
            }
        }

     stage('Download Latest SNAPSHOT WAR and Deploy') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'jfrog_art', 
                    usernameVariable: 'ART_USER', 
                    passwordVariable: 'ART_PASS'
                )]) {
                    script {
                        def baseUrl = "http://13.234.204.197:8081/artifactory/libs-snapshot-local"
                        def groupPath = "com/example/sample-webapp"
                        def artifactId = "sample-webapp"
                        def version = "1.6-SNAPSHOT"
                        def metadataUrl = "${baseUrl}/${groupPath}/${version}/maven-metadata.xml"

                        sh """
                            set -e

                            echo "Fetching Maven metadata from Artifactory..."
                            curl -u "\$ART_USER:\$ART_PASS" -s "${metadataUrl}" -o metadata.xml

                            echo "Parsing metadata.xml for timestamp and build number..."
                            TIMESTAMP=\$(grep -oPm1 '(?<=<timestamp>)[^<]+' metadata.xml)
                            BUILDNUM=\$(grep -oPm1 '(?<=<buildNumber>)[^<]+' metadata.xml)

                            echo "TIMESTAMP: \$TIMESTAMP"
                            echo "BUILD NUMBER: \$BUILDNUM"

                            WAR_NAME=${artifactId}-1.2-\${TIMESTAMP}-\${BUILDNUM}.war
                            ARTIFACT_URL=${baseUrl}/${groupPath}/${version}/\$WAR_NAME

                            echo "WAR to download: \$WAR_NAME"
                            echo "Downloading from: \$ARTIFACT_URL"

                            curl -u "\$ART_USER:\$ART_PASS" -o /tmp/\$WAR_NAME \$ARTIFACT_URL

                            echo "Stopping Tomcat..."
                            sudo /opt/tomcat/tomcat-11/bin/shutdown.sh || echo 'Tomcat may already be stopped'

                            echo "Removing old WAR..."
                            sudo rm -f /opt/tomcat/tomcat-11/webapps/${artifactId}.war

                            echo "Deploying new WAR to Tomcat webapps..."
                            sudo cp /tmp/\$WAR_NAME /opt/tomcat/tomcat-11/webapps/${artifactId}.war

                            echo "Starting Tomcat..."
                            sudo /opt/tomcat/tomcat-11/bin/startup.sh || echo 'Tomcat startup might need manual check'

                            echo "Deployment completed successfully: \$WAR_NAME"
                        """
                    }
                }
            }
        }
    }
} 
>>>>>>> ddcce6b20abe5c14ae172933251d74fe2c914b84
