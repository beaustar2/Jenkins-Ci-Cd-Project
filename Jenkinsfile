pipeline {
    agent {
        label 'node1'
    }

    stages {
        stage('SCM Checkout') {
            steps {
                script {
                    git tool: 'Default', credentialsId: 'git-creds', url: 'https://github.com/beaustar2/Jenkins-ci-cd-project.git', branch: 'master'
                }
            }
        }

        stage('Mvn Package') {
            steps {
                script {
                    def mvnHome = tool name: 'apache-maven-3.0.5', type: 'maven'
                    def mvnCMD = "${mvnHome}/bin/mvn"
                    
                    // Build and test in a single step
                    sh "${mvnCMD} clean package test"
                    stash(name: "Jenkins-ci-cd-project", includes: "target/*.war")
                }
            }
        }

        stage('Deploy Application') {
            agent {
                label 'node2'
            }
            steps {
                echo "Deploying the application"
                script {
                    def tomcatHome = "/home/centos/apache-tomcat-7.0.94"
                    def warDirectory = "${tomcatHome}/webapps"
                    
                    // Ensure the target directory exists
                    sh "mkdir -p ${warDirectory}"

                    // Deploy the WAR file
                    unstash "Jenkins-ci-cd-project"
                    sh "mv target/*.war ${warDirectory}/"

                    // Restart Tomcat
                    sh "sudo ${tomcatHome}/bin/startup.sh"
                }
            }
        }
    }

    post {
        success {
            script {
                mail to: "Sleekybee4@gmail.com",
                    subject: "Build and Deployment Successful - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: "Congratulations! The build and deployment were successful.\n\nCheck console output at ${env.BUILD_URL}"
            }
        }
        failure {
            script {
                mail to: "Sleekybee4@gmail.com",
                    subject: "Build and Deployment Failed - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: "Oops! The build and deployment failed.\n\nCheck console output at ${env.BUILD_URL}"
            }
        }
    }
}
