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
        sh "echo 'requested build version ${DESCRIPTION}-${VERSION}'"
        sh "echo 'login to docker hub...'"
        sh "echo '${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin'" // docker hub 로그인
      }
    }

    stage("build") {
      steps {
        sh "echo 'start building docker image...'"
        sh "echo 'docker build -t ${DOCKER_REPO}:${DOCKER_IMAGE_TAG} .'"
      }
    }

    stage("post-build") {
      steps {
        sh """
          echo 'pushing docker image...'
          echo 'docker push ${DOCKER_REPO}:${DOCKER_IMAGE_TAG}'
          echo '------------------------push success------------------------'
          cd helm
          sed -i "s/tag: 'change-me'/tag: '\$${DOCKER_IMAGE_TAG}'/" values.yaml
          cd ../
          echo 'updating helm chart...'
          git config user.email 'tingmon2@gmail.com'
          git config user.name 'tingmon2'
          git add .
          git commit -m 'refactor - updated Helm chart values for dev environment with container image - ${DOCKER_IMAGE_TAG}'
          echo 'helm push start'
          git push origin master
          echo 'helm push done'
          echo 'docker rmi ${DOCKER_REPO}:${DOCKER_IMAGE_TAG}'
        """
        // script {
        //   echo "shell script start"
        //   sh (
        //       """
        //       #!/bin/bash
        //       echo 'pushing docker image...'
        //       echo 'docker push ${DOCKER_REPO}:${DOCKER_IMAGE_TAG}'
        //       echo '------------------------push success------------------------'
        //       cd helm
        //       sed -i "s/tag: 'change-me'/tag: '\$${DOCKER_IMAGE_TAG}'/" values.yaml
        //       cd ../
        //       echo 'updating helm chart...'
        //       git config user.email 'tingmon2@gmail.com'
        //       git config user.name 'tingmon2'
        //       git add .
        //       git commit -m 'refactor - updated Helm chart values for dev environment with container image - ${DOCKER_IMAGE_TAG}'
        //       echo 'helm push start'
        //       git push origin master
        //       echo 'helm push done'
        //       echo 'docker rmi ${DOCKER_REPO}:${DOCKER_IMAGE_TAG}'
        //       """
        //   )
        // }
        // sh "echo 'pushing docker image...'"
        // sh "echo 'docker push ${DOCKER_REPO}:${DOCKER_IMAGE_TAG}'"
        // sh "echo '------------------------push success------------------------'"
        // sh "cd helm"
        // sed -i "s/tag: 'change-me'/tag: '\$${DOCKER_IMAGE_TAG}'/" values.yaml
        // sh "cd ../"
        // sh "echo 'updating helm chart...'"
        // sh "git config user.email 'tingmon2@gmail.com'"
        // sh "git config user.name 'tingmon2'"
        // sh "git add ."
        // sh "git commit -m 'refactor - updated Helm chart values for dev environment with container image - ${DOCKER_IMAGE_TAG}'"
        // sh "echo 'helm push start'"
        // sh "git push origin master"
        // sh "echo 'helm push done'"
        // sh "echo 'docker rmi ${DOCKER_REPO}:${DOCKER_IMAGE_TAG}'"
      }
    }
  }
}
