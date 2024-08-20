pipeline {
  agent {
    docker {
      image
      args
    }
  }

  stages {
    stage ('checkout') {
      steps {
        sh 'echo "Cloing the repo"'
        git branch: 'main', url: 'https://github.com/iam-veeramalla/Jenkins-Zero-To-Hero.git'
      }
    }
    stage ('building') {
      steps {
        sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn clean package'
          }
    }

    stage ('code quality') {
      environment {
        sonar_host = "http://127.0.0.0:9000"
      }
      steps {
        withCredentials([string(credentialsId: 'sonar', variable: 'SONAR_AUTH_TOKEN')]) {
          sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
        }
      }
    }
    stage ('Completed') {
           echo 'code has been packed and verified with sonarqube'
           }
}
