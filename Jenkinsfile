pipeline {
  agent any
  environment {
    DESCRIPTION = 'test'
    VERSION = '1.0.0'
    DOCKER_REPO = "tingmon/viewer-cicd"
    DOCKERHUB_CREDENTIALS = credentials('docker-tingmon') // jenkins에 등록해 놓은 docker hub credentials 이름
    DOCKER_IMAGE_TAG = "${DESCRIPTION}-${VERSION}"
  }

  stages {

    stage("pre-build") {
      steps {
        sh "cat /etc/*release"
        sh "apt-get -y install wget"
        sh "wget https://github.com/mikefarah/yq/releases/download/v4.12.0/yq_linux_amd64 -O /usr/bin/yq && chmod +x /usr/bin/yq"
        sh "yq --version"
        sh "echo 'requested build version ${DESCRIPTION}-${VERSION}'"
        sh "echo 'login to docker hub...'"
        sh "echo '${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin'" // docker hub 로그인
        sh "echo '------------------------login success------------------------'"
      }
    }

    stage("build") {
      steps {
        sh "echo 'start building docker image...'"
        sh "docker build -t ${DOCKER_REPO}:${DOCKER_IMAGE_TAG} ."
        sh "echo '------------------------build success------------------------'"
      }
    }

    stage("post-build") {
      steps {
        sh "echo 'pushing docker image...'"
        sh "docker push ${DOCKER_REPO}:${DOCKER_IMAGE_TAG}"
        sh "echo '------------------------push success------------------------'"
        sh "cd helm"
        sh "yq e -i '.image.tag = ${DOCKER_IMAGE_TAG}' ./values.yaml"
        sh "cd ../"
        sh "echo 'updating helm chart...'"
        sh "git config user.email 'tingmon2@gmail.com'"
        sh "git config user.name 'tingmon2'"
        sh "git add ."
        sh "git commit -m 'refactor - updated Helm chart values for dev environment with container image - ${DOCKER_IMAGE_TAG}'"
        sh "echo 'helm push start'"
        sh "git push origin master"
        sh "echo 'helm push done'"
        sh "docker rmi ${DOCKER_REPO}:${DOCKER_IMAGE_TAG}"
      }
    }
  }
}
