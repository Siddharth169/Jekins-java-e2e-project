pipeline {
  agent {
    docker {
      image 'abhishekf5/maven-abhishek-docker-agent:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
    }
  }

  stages {
    stage ('checkout') {
      steps {
        sh 'echo "Cloning the repo"'
        git branch: 'main', url: 'https://github.com/Siddharth169/CI-Jenkins-CD-Argocd.git'
      }
    }
    
    stage ('building') {
      steps {
        sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn clean package'
      }
    }

    stage ('code quality') {
      environment {
        sonar_host = "http://52.90.112.179:9000"
      }
      steps {
        withCredentials([string(credentialsId: 'sonar', variable: 'SONAR_AUTH_TOKEN')]) {
          sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=$sonar_host'
        }
      }
    }

    stage ('testing completed') {
      steps {
        echo 'Code has been packaged and verified with SonarQube'
      }
    }

    stage ('pushing the new image to Docker Hub') {
      environment {
        DOCKER_IMAGE = "siddharthmuruganandham/jenkins-springjava:${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = credentials('docker-pass')
      }
      steps {
        sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && docker build -t ${DOCKER_IMAGE} .'
        def dockerImage = docker.image("${DOCKER_IMAGE}")
        docker.withRegistry('https://index.docker.io/v1/', REGISTRY_CREDENTIALS) {
          dockerImage.push()
        }
      }
    }
  }
}
