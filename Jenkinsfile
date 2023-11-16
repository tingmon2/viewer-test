pipeline {
  agent any
  environment {
    DESCRIPTION = 'test'
    VERSION = '1.0.0'
    DOCKER_REPO = "tingmon/viewer-cicd"
    DOCKERHUB_CREDENTIALS = credentials('docker-tingmon')
    DOCKER_IMAGE_TAG = "${DESCRIPTION}-${VERSION}"
    TEMP_FOLDER = "/home/jenkins/agent/workspace/new-helm"
    GITHUB_CREDENTIALS = credentials('tingmon2')
  }

  stages {

    stage("pre-build") {
      steps {
        sh "cat /etc/*release"
        sh "pwd"
        sh "echo 'requested build version ${DESCRIPTION}-${VERSION}'"
        sh "echo 'login to docker hub...'"
        sh "echo '${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin'"
        echo '------------------------login success------------------------'
      }
    }

    stage("build") {
      steps {
        sh "echo 'start building docker image...'"
        sh "echo 'asdf | docker build -t ${DOCKER_REPO}:${DOCKER_IMAGE_TAG} .'"
        echo '------------------------build success------------------------'
      }
    }

    stage("post-build") {
      steps {
        sh """
          echo 'pushing docker image...'
          echo 'asdf | docker push ${DOCKER_REPO}:${DOCKER_IMAGE_TAG}'
          echo '------------------------push success------------------------'

          echo 'clone helm repo...'
          mkdir ${TEMP_FOLDER}
          cd ${TEMP_FOLDER}
          pwd
          ls -l
          git clone -b master 'https://tingmon2:${GITHUB_CREDENTIALS_PSW}@github.com/tingmon2/helm-viewer-test.git' ${TEMP_FOLDER}
          ls -l
          echo 'manipulate helm chart...'
          sed -i "s/tag: 'change-me'/tag: '${DOCKER_IMAGE_TAG}'/" values.yaml
          cat values.yaml

          git config user.email 'tingmon2@gmail.com'
          git config user.name 'tingmon2'
          git add .
          git commit -m 'refactor - updated Helm chart values for dev environment with container image - ${DOCKER_IMAGE_TAG}'

          echo 'helm push start'
          git push origin master
          echo 'helm push done'

          echo 'asdf | docker rmi ${DOCKER_REPO}:${DOCKER_IMAGE_TAG}'
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
