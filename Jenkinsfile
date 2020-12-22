node('chenyujie-jnlp') {
    stage('Prepare') {
        echo "1.Prepare Stage"
        checkout scm
        script {
            branch_name = sh(script: 'git rev-parse --abbrev-ref HEAD', returnStdout: true).trim()
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            if (branch_name != 'master') {
                build_tag = "${branch_name}-${build_tag}"
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
        if (branch_name == 'master') {
            echo "================================${branch_name}=================================="
            input "确认要部署线上环境吗？"
        }
        echo "================================${branch_name}=================================="
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${branch_name}/' k8s.yaml"
        sh "kubectl apply -f k8s.yaml --record"
    }
}
