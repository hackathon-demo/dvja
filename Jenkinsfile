pipeline {
  agent any

  tools {
    maven "apache-maven-3.6.3"
  }

  stages {
    stage('Build') {
      steps {
        git 'https://github.com/ajlanghorn/dvja.git'
        sh "mvn clean package"
      }
    }
    stage('Check dependencies') {
      steps {
        dependencyCheck additionalArguments: '', odcInstallation: 'Dependency-Check'
        dependencyCheckPublisher pattern: ''
      }
    }
    stage('Scan for vulnerabilities') {
      steps {
        sh 'java -jar dvja-*.war.jar -s http://ako20-appli-8lxm9c4oolv6-543855129.eu-west-1.elb.amazonaws.com/job/dvja/ && zap-cli quick-scan --self-contained --spider -r http://127.0.0.1 && zap-cli report -o zap-report.html -f html'
      }
    }
    stage('Publish to S3') {
      steps {
        sh "aws s3 cp /var/lib/jenkins/workspace/dvja/target/dvja-1.0-SNAPSHOT.war s3://ako2020-security-cicd-buildartifacts-1kj4kz6we45xw/dvja-1.0-SNAPSHOT.war"
      }
    }
    stage('Tidy up') {
      steps {
        cleanWs()
      }
    }
  }
     
  post {
    always {
      archiveArtifacts artifacts: 'zap-report.html', fingerprint: true
    }
  }
  
}
