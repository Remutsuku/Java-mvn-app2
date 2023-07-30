// update this scipt to match with  my pipeline for Maven-webapp-assignment

//used this to define my public ip to tomcatPublicIP so i can script it to send an email with a link to the webapp eg. (tomcatIP:8080/*targetfile)
def tomcatPublicIP = "65.2.69.80:8080"

pipeline {
    agent { label 'slave1' }

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven-3.6.3"
    }
        
    stages {
        stage('SCM Checkout') {
            steps {
                echo 'Perform SCM Checkout'
                git 'https://github.com/Remutsuku/Java-mvn-app2'
            }
        }

        stage('Build') {
            steps {
                echo 'Perform Build'
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
            post {
                failure {
                    echo 'Send mail on failure'
                    mail bcc: '', body: "Please go to http://${tomcatPublicIP}/mvn-hello-world/ and verify the build", cc: 'rocksings456@gmail.com', from: '', replyTo: '', subject: 'Build Notification', to: 'rocksings456@gmail.com'
                }
            }
        }

        stage('Compile') {
            steps {
                echo 'Perform Compile'
                sh "mvn compile"
            }
        }

        stage('Code Review') {
            steps {
                echo 'Perform Code Review'
                //Pauses the pipeline for code review
                input message: "Please review the code changes and approve:", ok: "Approve"
            }
        }

        stage('Unit Test') {
            steps {
                echo 'Perform Unit Test'
                sh "mvn test"
            }
        }

        stage('Package') {
            steps {
                echo 'Perform Package'
                sh "mvn package"
            }
        }

        stage('Deploy to Tomcat_Server') {
            steps {
                script {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'tomcat', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: 'target/', sourceFiles: 'target/mvn-hello-world.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                }
            }
            post {
                success {
                    echo 'Send mail on success'
                    mail bcc: '', body: "Please go to http://${tomcatPublicIP}/mvn-hello-world/ and verify the build", cc: 'rocksings456@gmail.com', from: '', replyTo: '', subject: 'Build Notification', to: 'rocksings456@gmail.com'
                    echo 'Build successful! Deployed to Tomcat.' 
                }
                failure {
                    echo 'Send mail on failure'
                    mail bcc: '', body: "Please go to http://${tomcatPublicIP}/mvn-hello-world/ and verify the build", cc: 'rocksings456@gmail.com', from: '', replyTo: '', subject: 'Build Notification', to: 'rocksings456@gmail.com'
                    echo 'Build failed. Deployment to Tomcat was not successful.'
                }
            }
        }
    }
}

