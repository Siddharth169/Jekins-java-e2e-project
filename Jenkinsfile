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
    stage ('pushing the new image to docker hub'){
      environment {
        DOCKER_IMAGE = "abhishekf5/ultimate-cicd:${BUILD_NUMBER}"
        // DOCKERFILE_LOCATION = "java-maven-sonar-argocd-helm-k8s/spring-boot-app/Dockerfile"
        REGISTRY_CREDENTIALS = credentials('docker-pass')
      }
      steps {
        sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && docker build -t ${DOCKER_IMAGE} .'
        def dockerImage = docker.image("${DOCKER_IMAGE}")
        docker.withRegistry('https://index.docker.io/v1/', "docker-pass") {
          dockerImage.push()
        }
      }
    }
  }
}
    
