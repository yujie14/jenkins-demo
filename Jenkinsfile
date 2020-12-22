node('chenyujie-jnlp') {
    stage('Prepare') {
        echo "1.Prepare Stage"
        checkout scm
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            if (env.GIT_BRANCH != 'master') {
                build_tag = "${env.GIT_BRANCH}-${build_tag}"
            }
        }
    }
    stage('Test') {
      echo "2.Test Stage"
    }
    stage('Build') {
        echo "3.Build Docker Image Stage"
        sh "docker build -t chenyujie19951114/jenkins-demo:${build_tag} ."
    }
    stage('Push') {
        echo "4.Push Docker Image Stage"
        withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
            sh "docker login -u ${dockerHubUser} -p ${dockerHubPassword}"
            sh "docker push chenyujie19951114/jenkins-demo:${build_tag}"
        }
    }
    stage('Deploy') {
        echo "5. Deploy Stage"
        if (env.GIT_BRANCH == 'master') {
            echo "================================${env.GIT_BRANCH}=================================="
            input "确认要部署线上环境吗？"
        }
        echo "================================${env.GIT_BRANCH}=================================="
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${env.GIT_BRANCH}/' k8s.yaml"
        sh "kubectl apply -f k8s.yaml --record"
    }
}
