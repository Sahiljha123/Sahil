pipeline {
    agent any
    environment {
        GITHUB_REPO = 'https://github.com/<your-username>/selenium-app.git'
        TOMCAT_URL = 'http://192.168.5.175:8080/manager/text'
        TOMCAT_CREDENTIALS_ID = 'c7cb4226-1fbd-4320-a11c-01b63e426fec	' // Jenkins credentials for Tomcat
        SELENIUM_HOST = 'http://192.168.5.82:4444/wd/hub'
        CONTEXT_PATH = '/selenium-app' // Change to your application's context path
    }
    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github-credentials', url: "${GITHUB_REPO}" // Pull from GitHub
            }
        }
        stage('Build WAR') {
            steps {
                sh 'mvn clean package' // This generates the WAR file
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                script {
                    // Get the artifact name from the pom.xml
                    def pom = readMavenPom file: 'pom.xml'
                    def warFileName = "target/${pom.artifactId}-${pom.version}.war"

                    // Deploy the WAR file to Tomcat
                    def deployCommand = "curl --user <username>:<password> --upload-file ${warFileName} ${TOMCAT_URL}/deploy?path=${CONTEXT_PATH}&update=true"
                    sh deployCommand
                }
            }
        }
        stage('Selenium Test') {
            steps {
                script {
                    // Run the Selenium test suite against the deployed application
                    sh '''
                    mvn -Dtest=RunSeleniumTests test
                    '''
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished!'
        }
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
